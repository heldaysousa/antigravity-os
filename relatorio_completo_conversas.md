# 🔬 Relatório Completo de Auditoria — Tela de Conversas
## Innflow · `/dashboard/conversations` · Antigravity OS
> **Escopo:** Rotas · Lógica · Configuração · Notificações · UX/UI · Sistema de Envio (WhatsApp + Instagram)
> **Data:** 2026-04-01 · **Investigador:** Antigravity OS

---

## 1. Visão Geral da Arquitetura

```
App.tsx (Lazy + ProtectedRoute + OnboardingGuard + DashboardLayout)
  └── /dashboard/conversations → <Conversations />
        ├── useRealtimeConversations(userId)      ← WebSocket Supabase
        ├── useRealtimeMessages(conversationId)   ← WebSocket Supabase
        ├── <OmnichannelView />                   ← Tabs WPP / Instagram
        ├── <ConversationList />                  ← Sidebar esquerda
        │     └── <ConversationCard />
        └── <ChatArea />                          ← Área central
              ├── <SmartReplies />                ← IA de sugestões
              ├── <MessageBubble />               ← Bolhas de mensagem
              └── handleSendMessage()
                    └── useOnboarding().sendMessage()
                          └── POST /functions/v1/send-whatsapp  ← SEMPRE (bug)
```

---

## BLOCO A — Roteamento e Configuração

### ✅ A1 — Proteção de Rota (OK)
A rota `/dashboard/conversations` é protegida por tripla camada de guards em `App.tsx`:
```
<ProtectedRoute>          ← verifica JWT Supabase
  <OnboardingGuard>       ← bloqueia se setup incompleto
    <DashboardLayout>     ← injeta sidebar + topbar
      <Conversations />
```
Arquitetura sólida. Sem falhas de segurança de rota.

### ✅ A2 — Lazy Loading (OK)
Todas as páginas são carregadas com `React.lazy()` + `<Suspense>`, com fallback de spinner.
Boa prática implementada corretamente.

### ✅ A3 — Deep Links via Query String (OK)
Múltiplos pontos do sistema apontam para a tela com parâmetros:
- `?conversation=ID` — vindo do Kanban
- `?id=ID` — vindo de notificações e widgets
- `?contact=ID` — vindo de cards de lead

A tela **não lê esses parâmetros** (`useSearchParams` ausente). Os deep links chegam mas são ignorados.

> [!WARNING]
> **BUG DE CONFIGURAÇÃO:** `useSearchParams` não implementado em `Conversations.tsx`.
> Clicar em "Ver conversa" em notificações, Kanban e widgets abre a tela mas não
> seleciona automaticamente a conversa correta. O usuário precisa procurar manualmente.

---

## BLOCO B — Lógica de Dados e Realtime

### ✅ B1 — Realtime WebSocket (OK com ressalvas)
Dois canais WebSocket independentes via Supabase Realtime:
- `conversations:{userId}` — escuta INSERT/UPDATE/DELETE de conversas
- `messages:{conversationId}` — escuta INSERT/UPDATE/DELETE de mensagens

Ambos têm lógica de **retry automático** a cada 3s em caso de `CHANNEL_ERROR`, `CLOSED` ou `TIMED_OUT`. Boa resiliência.

### ✅ B2 — Enriquecimento de Dados (OK com custo)
`useRealtimeConversations` faz dois SELECTs a cada carregamento:
1. Busca as conversas paginadas
2. Busca **todos** os contatos do usuário (sem paginação)

Os dados são cruzados em memória para adicionar `lead_score`, `temperature`, `segment` e `qualified` às conversas.

> [!WARNING]
> **PROBLEMA DE PERFORMANCE:** A query de contatos não tem limite (`select('*').eq('user_id')`)
> e carrega o array inteiro na memória. Para usuários com 1.000+ contatos, isso pode gerar
> payloads de centenas de KB a cada abertura da tela.

### 🔴 B3 — Cache de Mensagens no LocalStorage (CRÍTICO)
**Localização:** `hooks/useRealtime.ts` linhas 87–90, 156–158

```typescript
// A cada nova mensagem recebida via WebSocket:
localStorage.setItem(cacheKey.current, JSON.stringify(updated)); // ← serializa tudo
```

O array de mensagens inteiro é serializado de forma **síncrona no Main Thread** a cada nova mensagem.

**Impacto:**
- Congelamento do input de digitação em conversas longas (100+ mensagens)
- `QuotaExceededError` do browser quando o cache ultrapassa ~5MB por domínio
- Risco de corrupção de cache se o browser travar durante o `setItem`

### ✅ B4 — Filtragem e Ordenação (OK)
Filtros implementados corretamente em `useMemo`:
- Status, fonte, canal, busca semântica, segmento, sentimento, temperatura
- Ordenação por prioridade: hot leads → lead score → data de atualização
- Deduplicação de filtros `source` vs `channel` (redundante mas funcional)

---

## BLOCO C — Sistema de Notificações

### ✅ C1 — Push Notification do Sistema (OK)
`ChatArea.tsx` dispara Web Push Notification quando:
```typescript
if (lastMessage && lastMessage.sender !== "user" && document.hidden) {
  showNotification(contact_name, content, phone);
}
```
Lógica correta: só notifica quando a aba está em background e o remetente é o contato.

### ✅ C2 — Som de Nova Mensagem (OK)
`useRealtime.ts` dispara `playPop()` via `useSystemSounds` quando o evento WebSocket
é um INSERT com `sender === 'contact'`. Feedback de audio correto e bem posicionado.

### ✅ C3 — Gamificação de Vendas (OK)
`useRealtimeSales` escuta o evento broadcast `SALE_APPROVED` e dispara:
- Som de caixa registradora (`playCashRegister()`)
- Confetti animado na tela via `canvas-confetti`
Implementação de engajamento funcional.

### 🟡 C4 — Typing Indicator com Lógica Invertida (MÉDIO)
**Localização:** `ChatArea.tsx` linhas 301–324

O componente exibe "Você está digitando..." baseado no estado do `inputValue` do próprio usuário logado.
O `useTypingIndicator` deveria mostrar quando **o contato** está digitando, não o próprio agente.

```tsx
{isTyping && (
  <div>
    <span>Você está digitando...</span>  // ← texto errado + lógica invertida
  </div>
)}
```

**Impacto:** Confunde o usuário — o texto "Você está digitando" aparece enquanto o próprio
agente digita, o que é obviamente redundante. O indicador deveria mostrar "Contato está digitando...".

### 🟡 C5 — Smart Replies com Fallback Estático (BAIXO)
**Localização:** `SmartReplies.tsx` linhas 58–73

Quando a Edge Function `chat-ai` falha ou retorna vazio, o componente sempre exibe as mesmas 3 sugestões genéricas:
```
"Obrigado pelo contato!"
"Como posso ajudar?"
"Vou verificar isso para você."
```
Para qualquer mensagem recebida — inclusive "Quero cancelar" ou "Estou insatisfeito". Pode ser inadequado contextualmente.

---

## BLOCO D — UX e UI

### 🟠 D1 — Deep Links Ignorados (ALTO)
Já detalhado em A3. Quando o usuário clica em "Ver conversa" em qualquer widget, notificação
ou card do Kanban, a tela de Conversas abre sem selecionar a conversa correspondente.
O parâmetro `?id=`, `?conversation=` e `?contact=` chegam pela URL mas nenhum código os lê.

### 🟠 D2 — Avatar Numérico para Contatos sem Nome
**Localização:** `ConversationCard.tsx` linha 100–102

```typescript
const displayName = conversation.contact_name || conversation.contact_phone || "Desconhecido";
const firstLetter = displayName.charAt(0).toUpperCase(); // → "5" para "5511999..."
```

Contatos que chegaram sem nome (via Evolution API sem `pushName`) ficam representados pela
letra "5" (primeiro dígito do DDI brasileiro). Em produção com muitas conversas anônimas,
a lista fica cheia de avatares "5".

**Correção:** Usar ícone genérico de pessoa quando não há nome, ou gerar iniciais do número
formatado como `+55 (11) 9999...`.

### 🟡 D3 — Contraste Visual Insuficiente (MÉDIO)
Observado na captura de tela. O layout usa:
- Background principal: `#0a0a0a`
- Cards: `#13141a/80` com `border-white/5`

A diferença de luminosidade entre o fundo e os painéis é muito baixa (~3%). O Liquid Glass
fica invisível em monitores com brilho menor. A área de "Nenhuma conversa selecionada"
flutua sem hierarquia visual.

**Correção:** Aumentar `border-white/10` → `border-white/15` e adicionar:
```css
box-shadow: 0 0 0 1px rgba(255,255,255,0.06), 0 8px 32px rgba(0,0,0,0.4);
```

### 🟡 D4 — Empty State do Chat sem CTA (BAIXO)
Quando nenhuma conversa está selecionada, o estado vazio mostra apenas ícone + texto.
Não há botão de ação ou sugestão do que fazer (ex: "Conecte seu WhatsApp" se não há conversas,
ou "Selecione uma conversa à esquerda para começar").

### 🟡 D5 — Sidebar do Chat Duplicada com AIContextPanel
**Localização:** `Conversations.tsx` linhas 254–280

O `ChatArea` tem sua própria `<ConversationSidebar>` (controlada por `showSidebar`),
E o `Conversations.tsx` renderiza por fora `<AIContextPanel>`, `<AIProposalsPanel>`, `<LeadDetails>` e `<ConversationalTicket>`.

Isso cria dois painéis laterais diferentes operando em paralelo, com controles distintos (`showSidebar` vs `showAIContext`). Em telas médias (`xl`) há sobreposição de scroll.

---

## BLOCO E — Sistema de Envio de Mensagens (Investigação Principal)

### Cadeia de Chamadas Completa

```
[1] ChatArea.tsx → handleSendMessage()
[2]   └── useOnboarding().sendMessage(phone, msg, source, mediaUrl, type)
[3]         ├── Busca conversa: .eq("contact_phone", phone)  ← BUG #2
[4]         └── fetch('/functions/v1/send-whatsapp')         ← BUG #1 (sempre WPP)
[5]               ├── Verifica limite de mensagens do plano
[6]               ├── Busca instância WPP conectada
[7]               ├── Chama Evolution API → WhatsApp
[8]               └── Salva mensagem + atualiza conversa no DB
```

### 🔴 E1 — Instagram Completamente Ignorado no Envio (CRÍTICO)
**Localização:** `hooks/useOnboarding.ts` linha ~295

O parâmetro `source` é recebido mas **nunca usado** para rotear o canal correto:
```typescript
// BUGADO: sempre vai para send-whatsapp independente do canal
const response = await fetch(
  `${SUPABASE_URL}/functions/v1/send-whatsapp`,  // ← hardcoded
  { ... }
);
```

A Edge Function `instagram-send` **existe e está implementada** (`supabase/functions/instagram-send/index.ts`)
mas **nunca é chamada** pelo frontend. O contrato correto ela espera:
```json
{ "tenant_id": "user_id", "to": "instagram_user_id", "text": "mensagem" }
```

**Impacto:**
- Instagram 100% inoperante para envio
- Mensagens tentam ir via Evolution API com ID inválido de Instagram
- Cota de mensagens do plano é consumida sem entrega
- Usuário não recebe nenhum erro — apenas o chat permanece vazio (Bug E3)

**Correção:**
```typescript
const isInstagram = source === "instagram";
const endpoint = isInstagram
  ? `${SUPABASE_URL}/functions/v1/instagram-send`
  : `${SUPABASE_URL}/functions/v1/send-whatsapp`;

const payload = isInstagram
  ? { tenant_id: user.id, to: phone, text: message }
  : { phone, message, conversation_id: conversationId, user_id: user.id,
      media_url: mediaUrl, media_type: messageType };
```

### 🔴 E2 — Query com Coluna Errada Gera Conversas Duplicadas (CRÍTICO)
**Localização:** `hooks/useOnboarding.ts` linhas 265–270

```typescript
// BUGADO
.eq("contact_phone", phone)  // coluna incorreta

// O schema real (conforme evolution-webhook/index.ts):
// INSERT usa "phone" como coluna principal
```

Como a query nunca encontra a conversa existente, o `sendMessage` **sempre cria uma nova**,
gerando duplicatas com dados incompletos (sem `contact_id`, `contact_name` = número bruto).

**Correção:** `.eq("phone", phone)` ou adicionar fallback:
```typescript
.or(`phone.eq.${phone},contact_phone.eq.${phone}`)
```

### 🔴 E3 — Mensagem Enviada Não Aparece no Chat (CRÍTICO)
**Localização:** `ChatArea.tsx` linhas 200–214

```typescript
if (result) {
  setInputValue("");
  removeFile();
  success("Mensagem enviada com sucesso!");
  // ← ZERO linhas adicionam a mensagem ao estado local
}
```

A mensagem só apareceria quando o webhook da Evolution devolvesse `messages.upsert` com `fromMe: true`.
Se o webhook não estiver configurado ou houver latência, a mensagem **nunca aparece na tela**.

**Impacto:**
- Chat parece não ter enviado nada
- Usuário reenvia → duplicação de mensagens para o contato
- Em instâncias sem webhook configurado, o chat fica permanentemente vazio

**Correção — inserção otimista imediata:**
```typescript
// Após result === true:
const optimisticMessage = {
  id: `pending-${Date.now()}`,
  conversation_id: conversation.id,
  content: inputValue,
  sender: "user" as const,
  timestamp: new Date().toISOString(),
  delivery_status: "sent" as const,
};
// Injetar via prop: onOptimisticMessage(optimisticMessage)
// O WebSocket substitui pelo registro real quando chegar
```

### 🟠 E4 — Erros Engolidos sem Contexto para o Usuário (ALTO)
**Localização:** `useOnboarding.ts` catch + `ChatArea.tsx` catch

```typescript
// useOnboarding.ts
} catch (err) {
  console.error("Error sending message:", err);
  return false;  // ← detalhes do erro perdidos
}

// ChatArea.tsx — mensagem sempre genérica
showError("Erro ao enviar mensagem. Tente novamente.");
```

A Edge Function `send-whatsapp` retorna erros semânticos ricos que são descartados:
- `limit_reached: true` → HTTP 429
- `"No connected WhatsApp instance found"` → HTTP 400
- `"Evolution API error: 401"` → credenciais inválidas

**Correção:**
```typescript
// useOnboarding.ts — propagar erro
const data = await response.json();
if (!response.ok) {
  throw new Error(data.error || "Erro desconhecido", { cause: data });
}
```

### 🟠 E5 — Sem Alerta Contextual para WhatsApp Desconectado (ALTO)
**Localização:** `supabase/functions/send-whatsapp/index.ts` L129–137

A Edge Function detecta e retorna erro claro quando não há instância conectada,
mas o frontend não diferencia esse caso.

O usuário receberá sempre "Erro ao enviar mensagem" sem saber que precisa reconectar
o WhatsApp em Configurações → Integrações.

### 🟡 E6 — `instagram-send` com Ambiguidade `tenant_id` vs `user_id` (MÉDIO)
A Edge Function do Instagram usa `tenant_id` para lookup de `instagram_accounts`.
Na arquitetura do Innflow, `tenant_id = user_id`, mas isso não está documentado nem
há validação explícita. Se `instagram_accounts` foi criada com coluna diferente, falha silenciosamente.

### 🟡 E7 — Schema Inconsistente: `phone` vs `contact_phone` (MÉDIO)

| Camada | Campo usado | Status |
|--------|------------|--------|
| `evolution-webhook` INSERT conversations | `phone` (principal) | ✅ correto |
| `useOnboarding.sendMessage` query | `contact_phone` | ❌ errado |
| `useOnboarding.sendMessage` INSERT | `contact_phone` sem `phone` | ❌ incompleto |
| `useRealtimeConversations` READ | `conv.phone` | ✅ correto |
| `instagram-send` query | `phone` | ✅ correto |

Dois campos para o mesmo dado criando inconsistência entre o que o webhook salva e o que o frontend lê.

---

## BLOCO F — Segurança

### ✅ F1 — Webhook com Sanitização de Input (OK)
`evolution-webhook` sanitiza strings removendo `< > " '` antes de processar.
Tem validação de campos obrigatórios (`event`, `instance`) e tipos.

### 🟡 F2 — Autenticação do Webhook Desativada (MÉDIO)
**Localização:** `evolution-webhook/index.ts` linhas 44–52

```typescript
// Temp disable signature check
// const EVOLUTION_API_KEY = Deno.env.get("EVOLUTION_API_KEY");
// if (!EVOLUTION_WEBHOOK_SECRET && (!EVOLUTION_API_KEY || apikey !== EVOLUTION_API_KEY)) {
//   return ... 401 Unauthorized
// }
```

A validação de API Key **está comentada** com "Temp disable". O endpoint do webhook
aceita qualquer requisição POST sem autenticação.

**Impacto:** Qualquer pessoa que descobrir a URL do webhook pode injetar mensagens falsas
no sistema, criar conversas e contaminar dados de contatos.

### ✅ F3 — Rate Limiting no Webhook (OK)
O webhook tem rate limiting via `supabase.rpc("check_rate_limit")`: 100 req/min por tenant.
Implementação correta, embora a autenticação desativada (F2) mitigue este benefício.

### ✅ F4 — Limite de Mensagens por Plano (OK)
`send-whatsapp` verifica `client_usage` vs `plan_limits` antes de enviar. HTTP 429 com
`limit_reached: true` quando atingido. Implementação correta.

---

## 📊 Tabela Consolidada de Todos os Problemas

| # | Bloco | Severidade | Arquivo Principal | Problema | Impacto |
|---|-------|-----------|------------------|---------|---------|
| E1 | Envio | 🔴 CRÍTICO | `useOnboarding.ts` | Instagram ignorado — sempre rota WPP | Instagram 100% inoperante |
| E2 | Envio | 🔴 CRÍTICO | `useOnboarding.ts` | `.eq("contact_phone")` coluna errada | Duplicação de conversa a cada envio |
| E3 | Envio | 🔴 CRÍTICO | `ChatArea.tsx` | Mensagem não aparece após envio bem-sucedido | UX quebrada — usuário reenvia duplicado |
| B3 | Dados | 🔴 CRÍTICO | `useRealtime.ts` | Cache de mensagens síncrono no localStorage | Freeze do browser + QuotaExceededError |
| A3 | Rotas | 🟠 ALTO | `Conversations.tsx` | Deep links `?id=` e `?conversation=` ignorados | Clique em notificação não abre conversa |
| E4 | Envio | 🟠 ALTO | Hook + ChatArea | Erros engolidos sem contexto | Usuário não sabe o que corrigir |
| E5 | Envio | 🟠 ALTO | `send-whatsapp` EF | Sem alerta específico para WPP desconectado | Usuário não sabe reconectar |
| D1 | UX | 🟠 ALTO | `Conversations.tsx` | Deep links não implementados | Navegação quebrada vinda de outros módulos |
| B2 | Dados | 🟠 ALTO | `useRealtime.ts` | Query de contatos sem limit carrega tudo | Performance degradada para +1k contatos |
| F2 | Seg. | 🟡 MÉDIO | `evolution-webhook` | Validação de API Key comentada | Webhook aberto para injeção de dados |
| C4 | UX | 🟡 MÉDIO | `ChatArea.tsx` | Typing indicator lógica invertida | "Você está digitando" mostrado para o próprio agente |
| D2 | UX | 🟡 MÉDIO | `ConversationCard.tsx` | Avatar exibe "5" para números sem nome | Lista de contatos ilegível em produção |
| D3 | UI | 🟡 MÉDIO | CSS global | Contraste insuficiente Liquid Glass | Hierarquia visual perdida em monitores escuros |
| D5 | UX | 🟡 MÉDIO | `Conversations.tsx` | Dois painéis laterais paralelos | Scroll conflitante em telas médias |
| E6 | Envio | 🟡 MÉDIO | `instagram-send` EF | `tenant_id` vs `user_id` ambíguo | Contas Instagram podem falhar |
| E7 | Schema | 🟡 MÉDIO | Geral | `phone` vs `contact_phone` inconsistente | Queries quebradas + duplicação |
| C5 | UX | 🔵 BAIXO | `SmartReplies.tsx` | Fallback de sugestões sempre genérico | Sugestões inadequadas para contexto negativo |
| D4 | UI | 🔵 BAIXO | `Conversations.tsx` | Empty state sem CTA | Usuário sem orientação em tela vazia |

---

## 🛠️ Plano de Correção por Prioridade

### Sprint 1 — Funcionalidade Core (Bugs que travam o uso)
1. **E3** — Inserção otimista de mensagem no estado local após envio
2. **E2** — Corrigir `.eq("contact_phone")` → `.eq("phone")` em `sendMessage`
3. **E1** — Roteamento condicional WhatsApp vs Instagram no `sendMessage`
4. **B3** — Substituir cache síncrono do localStorage por cache assíncrono (IndexedDB ou apenas session)
5. **A3/D1** — Implementar `useSearchParams` para ler `?id=` e `?conversation=`

### Sprint 2 — Resiliência e Feedback
6. **E4** — Propagar erros semânticos da Edge Function até o toast do usuário
7. **E5** — Toast específico para WPP desconectado com link para Configurações
8. **F2** — Reativar validação de API Key no `evolution-webhook`
9. **C4** — Corrigir lógica e texto do Typing Indicator
10. **B2** — Adicionar `.limit(500)` na query de contatos + paginação lazy

### Sprint 3 — Polimento UX/UI e Schema
11. **D2** — Avatar com ícone genérico para contatos sem nome
12. **D3** — Aumentar contraste do Liquid Glass (`border-white/15` + shadow)
13. **E7** — Padronizar coluna `phone` em toda a aplicação
14. **E6** — Documentar e validar `tenant_id = user_id` para Instagram
15. **C5** — Melhorar fallback de Smart Replies com sugestões por sentimento
16. **D4** — Adicionar CTA ao empty state
17. **D5** — Unificar painéis laterais em um único componente controlado

---

> [!IMPORTANT]
> **Prioridade máxima:** E1 + E2 + E3 — esses três bugs juntos tornam o envio de mensagens
> completamente inoperante tanto para WhatsApp (UX quebrada) quanto para Instagram (100% não funciona).

> [!CAUTION]
> **F2 (Webhook sem auth)** deve ser corrigido em paralelo antes do lançamento em produção.
> Um endpoint público de webhook sem validação é um vetor de injeção de dados no banco.
