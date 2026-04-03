# 🔬 Relatório de Auditoria: Sistema de Envio de Mensagens
### Innflow — Tela `/dashboard/conversations`
> Investigação completa: Frontend → Hook → Edge Functions → APIs externas

---

## Arquitetura Geral do Sistema de Envio

```
ChatArea.tsx
  └── handleSendMessage()
        └── useOnboarding().sendMessage(phone, message, source, mediaUrl, type)
              └── [WhatsApp] POST /functions/v1/send-whatsapp
                    └── Evolution API → WhatsApp ✅
              └── [Instagram] ??? ← PROBLEMA CRÍTICO (nunca chamado)
```

**Edge Functions identificadas no projeto:**
- `send-whatsapp` — Envia via Evolution API (WhatsApp)
- `instagram-send` — Envia via Instagram DM Service (**existe mas nunca é chamada**)
- `evolution-webhook` — Recebe mensagens da Evolution API (inbound)
- `evolution-instance-manager` — Gerencia instâncias WhatsApp (QR, connect, status)

---

## 🔴 BUG #1 — CRÍTICO: Instagram completamente ignorado no sendMessage

**Localização:** `apps/client/src/hooks/useOnboarding.ts` → `sendMessage()` linha ~295

**O Problema:**
O parâmetro `source` ("whatsapp" ou "instagram") é recebido, mas **100% ignorado**.
Toda mensagem, independente do canal, vai para `/functions/v1/send-whatsapp`.

```typescript
// SEMPRE chama send-whatsapp, mesmo para Instagram
const response = await fetch(
  `${SUPABASE_URL}/functions/v1/send-whatsapp`,  // ← hardcoded
  { ... }
);
```

A Edge Function `instagram-send` **existe e está implementada**, mas nunca é invocada pelo frontend.

**Impacto:** Respostas de Instagram são enviadas para a Evolution API (WhatsApp) com um
identificador inválido. A mensagem **falha silenciosamente** e a cota do plano é consumida sem entrega.

**Correção:**
```typescript
const endpoint = source === "instagram"
  ? `${SUPABASE_URL}/functions/v1/instagram-send`
  : `${SUPABASE_URL}/functions/v1/send-whatsapp`;

const payload = source === "instagram"
  ? { tenant_id: user.id, to: phone, text: message }
  : { phone, message, conversation_id: conversationId, user_id: user.id };
```

---

## 🔴 BUG #2 — CRÍTICO: Query usa coluna errada para buscar conversa existente

**Localização:** `apps/client/src/hooks/useOnboarding.ts` → `sendMessage()` linhas 265–270

**O Problema:**
```typescript
const { data: existingConv } = await supabase
  .from("conversations")
  .select("id")
  .eq("user_id", user.id)
  .eq("contact_phone", phone)  // ← COLUNA ERRADA
  .maybeSingle();
```

O webhook da Evolution salva conversas com a coluna `phone`, não `contact_phone`:
```typescript
// evolution-webhook/index.ts L269–278
.insert({
  user_id: userId,
  contact_id: contact.id,
  phone,              // ← coluna real
  contact_phone: phone,
  ...
})
```

**Impacto:** O `sendMessage` **nunca encontra** a conversa existente → cria novas duplicadas a cada
envio do agente, sem `contact_id` correto, gerando histórico fragmentado.

**Correção:** Mudar para `.eq("phone", phone)` na query de busca.

---

## 🔴 BUG #3 — CRÍTICO: Mensagem enviada não aparece no chat

**Localização:** `apps/client/src/components/conversations/ChatArea.tsx` → `handleSendMessage()`

**O Problema:**
Após envio bem-sucedido, o frontend apenas limpa o input e mostra toast — nenhuma mensagem é adicionada ao estado local:
```typescript
if (result) {
  setInputValue("");
  removeFile();
  success("Mensagem enviada com sucesso!");
  // ← NADA adiciona a mensagem à lista do chat
}
```

A mensagem só apareceria quando o webhook da Evolution devolvesse `messages.upsert` com `fromMe: true`.
Se o webhook estiver desconfigurado, lento ou offline, **a mensagem nunca aparece**.

**Impacto:** O chat parece não ter enviado nada. UX quebrada — usuário reenvia, gerando mensagens duplicadas.

**Correção — inserção otimista:**
```typescript
// Após sucesso, injetar imediatamente no estado
const optimisticMsg = {
  id: `optimistic-${Date.now()}`,
  conversation_id: conversation.id,
  content: inputValue,
  sender: "user",
  timestamp: new Date().toISOString(),
  delivery_status: "sent",
};
// Via prop callback: onOptimisticMessage(optimisticMsg)
```

---

## 🟠 BUG #4 — ALTO: Erros lançados sem contexto para o usuário

**Localização:** `useOnboarding.ts` catch + `ChatArea.tsx` catch

**O Problema:**
```typescript
// useOnboarding.ts — catch silencioso sem rethrow
} catch (err) {
  console.error("Error sending message:", err);
  return false;  // ← detalhe perdido
}

// ChatArea.tsx — toast sempre genérico
showError("Erro ao enviar mensagem. Tente novamente.");
```

A Edge Function já retorna erros ricos: `limit_reached: true`, `"No connected WhatsApp instance found"`.
Mas todos são descartados no catch silencioso.

**Impacto:** O usuário não sabe se é limite do plano, WPP desconectado, Instagram não configurado ou falha de rede.

---

## 🟠 BUG #5 — ALTO: Nenhum alerta específico quando WhatsApp desconectado

**Localização:** `supabase/functions/send-whatsapp/index.ts` L129–137

**O Problema:**
A Edge Function retorna HTTP 400 claro quando não há instância WPP conectada, mas o frontend
trata com o mesmo toast genérico do Bug #4. O usuário deveria receber "Seu WhatsApp está desconectado. Reconecte em Configurações."

---

## 🟡 BUG #6 — MÉDIO: `instagram-send` com ambiguidade `tenant_id` vs `user_id`

**Localização:** `supabase/functions/instagram-send/index.ts` L14, 32–36

**O Problema:**
A função espera `tenant_id` para buscar `instagram_accounts`, mas em toda a arquitetura
do Innflow `tenant_id = user_id`. Não há garantia de que a tabela `instagram_accounts`
foi populada com esse mapeamento, podendo causar erros silenciosos quando o Instagram for ativado.

---

## 🟡 BUG #7 — MÉDIO: Schema inconsistente `phone` vs `contact_phone`

| Localização | Campo usado | Observação |
|-------------|------------|------------|
| `evolution-webhook` INSERT | `phone` + `contact_phone` | ambos preenchidos |
| `useOnboarding.sendMessage` query | `contact_phone` | **errado** |
| `useOnboarding.sendMessage` INSERT | `contact_phone` (sem `phone`) | incompleto |
| `useRealtimeConversations` enrich | `conv.phone` | correto |
| `instagram-send` query | `phone` | correto |

Dois campos para o mesmo dado, usados de formas diferentes em cada camada.

---

## 📊 Resumo Completo

| # | Severidade | Arquivo | Problema | Impacto |
|---|-----------|---------|---------|---------|
| 1 | 🔴 CRÍTICO | `useOnboarding.ts` | Instagram nunca roteado — hardcoded WPP | Instagram 100% quebrado |
| 2 | 🔴 CRÍTICO | `useOnboarding.ts` | `.eq("contact_phone")` coluna errada | Conversa duplicada a cada envio |
| 3 | 🔴 CRÍTICO | `ChatArea.tsx` | Mensagem não aparece após envio bem-sucedido | UX quebrada, usuário reenvia duplicado |
| 4 | 🟠 ALTO | Hook + ChatArea | Erros engolidos sem contexto | Usuário não sabe o que corrigir |
| 5 | 🟠 ALTO | `send-whatsapp` EF | Sem alerta específico para WPP desconectado | Usuário não sabe reconectar |
| 6 | 🟡 MÉDIO | `instagram-send` EF | `tenant_id` vs `user_id` ambíguo | Pode falhar ao ativar Instagram |
| 7 | 🟡 MÉDIO | Schema geral | `phone` vs `contact_phone` inconsistente | Queries quebradas e duplicação |

---

## 🛠️ Plano de Correção

### Fase 1 — Causa raiz (corrigir agora)
1. **Bug #3** → Inserção otimista da mensagem no estado local (`ChatArea.tsx`)
2. **Bug #2** → Corrigir `.eq("contact_phone")` → `.eq("phone")` em `sendMessage`
3. **Bug #1** → Roteamento condicional WPP vs Instagram no `sendMessage`

### Fase 2 — Resiliência
4. **Bug #4** → Propagar erro semântico da Edge Function até o toast do usuário
5. **Bug #5** → Toast específico para WPP desconectado com link para Settings

### Fase 3 — Schema e consistência
6. **Bug #7** → Padronizar coluna única `phone` em toda a aplicação
7. **Bug #6** → Documentar e validar `tenant_id = user_id` para Instagram
