# Rastreamento completo: Billing / ASAAS / Criação de clientes e assinaturas (App Master)

## 1. OVERVIEW GERAL DO FLUXO

### 1.1 Pontos de entrada (rotas e processos)

| Camada | Arquivo / Rota | O que faz |
|--------|----------------|-----------|
| **Client** | `apps/client/src/App.tsx` | Rota `/dashboard/billing` → componente `Billing` |
| **Client** | `apps/client/src/pages/dashboard/Billing.tsx` | Página Billing: exibe planos, uso, botão "Fazer Upgrade" |
| **Client** | `Billing.tsx` → `handleUpgrade(planId, 'PIX')` | Chama `supabase.functions.invoke('asaas-checkout', { body: { plan, billingType } })` |
| **Supabase** | Edge Function `supabase/functions/asaas-checkout/index.ts` | Recebe JWT, cria/atualiza cliente ASAAS, assinatura, grava em `asaas_customers`, `asaas_subscriptions`, `subscriptions` |
| **Supabase** | Edge Function `supabase/functions/asaas-webhook/index.ts` | Recebe eventos ASAAS (pagamento/assinatura), atualiza `asaas_payments`, `asaas_subscriptions`, `subscriptions` |
| **Client** | `useSubscription` / `getSubscription` | Lê da tabela `subscriptions` (e `plan_limits`) para exibir plano atual e limites |
| **Client** | `useOnboarding().getSubscription()` | Lê `subscriptions` com `.eq('status', 'active')` + `plan_limits` |

### 1.2 Fluxo de dados (checkout)

```
[Usuário clica Fazer Upgrade]
  → Billing.tsx handleUpgrade()
  → supabase.functions.invoke('asaas-checkout', { body: { plan, billingType } })
  → Supabase anexa Authorization: Bearer <JWT> automaticamente se usuário logado

[asaas-checkout Edge Function]
  1. Valida JWT (getUser)
  2. Busca asaas_customers por user_id
     - Se existe: usa asaas_id
     - Se não: POST /customers na API ASAAS → insert em asaas_customers
  3. Busca asaas_subscriptions por user_id + status ACTIVE
     - Se existe: PUT /subscriptions/:id na ASAAS → update asaas_subscriptions → return { message: "Subscription updated" }
     - Se não: POST /subscriptions na ASAAS → insert asaas_subscriptions → upsert subscriptions
  4. GET /payments?subscription=:id (ASAAS) → opcionalmente GET /payments/:id/pixQrCode
  5. Return { success, subscriptionId, customerId, payment: { invoiceUrl, bankSlipUrl, pixQrCode, pixCopyPaste } }
```

### 1.3 Tabelas envolvidas

| Tabela | Uso |
|--------|-----|
| `subscriptions` | Uma linha por usuário (UNIQUE user_id). Colunas: id, user_id, plan, status, created_at, updated_at, asaas_subscription_id, asaas_customer_id. **Fonte de verdade no app** para plano/status. |
| `asaas_customers` | Mapeamento user_id ↔ asaas_id (cliente no ASAAS). |
| `asaas_subscriptions` | Cópia das assinaturas ASAAS (asaas_id, plan, status, value, next_due_date, etc.). |
| `asaas_payments` | Pagamentos ASAAS (preenchido pelo webhook). |
| `plan_limits` | Limites por plano (plan, max_messages_per_month, max_contacts, etc.). |

### 1.4 Possíveis causas de “não está sendo possível gerar”

1. **Upsert em `subscriptions` sem `onConflict`**  
   A tabela tem `UNIQUE(user_id)`. O código atual faz `upsert({ user_id, plan, status, ... })` sem `onConflict: 'user_id'`. No PostgREST/Supabase o upsert padrão é por primary key (`id`). Como não se envia `id`, o comportamento é de INSERT; ao existir já uma linha para esse `user_id`, falha por violação de UNIQUE.  
   **Correção:** usar `upsert(..., { onConflict: 'user_id' })`.

2. **Secrets do Supabase não configurados**  
   Se `ASAAS_API_KEY` (ou `ASAAS_API_URL`) não estiver definido nas Edge Functions, qualquer chamada à API ASAAS falha (e a função retorna erro genérico).

3. **JWT não enviado ou inválido**  
   Se o client não estiver logado ou o token expirado, `getUser()` na Edge Function falha → "Unauthorized".

4. **Variáveis de ambiente no client**  
   Se `VITE_SUPABASE_URL` ou `VITE_SUPABASE_ANON_KEY` (ou publishable) estiverem errados, `supabase.functions.invoke` pode apontar para outro projeto ou falhar.

5. **RLS / políticas**  
   Usuário só vê a própria subscription; service_role escreve em todas. Se a Edge Function usar service_role corretamente, não costuma ser bloqueio.

6. **API ASAAS (sandbox/produção)**  
   URL errada, key de outro ambiente ou conta inativa podem fazer todas as chamadas ASAAS falharem.

---

## 2. DECOMPOSIÇÃO (SPLITTER-AGENT)

Input: “Rastreamento completo entre rotas e processos de criação de clientes e assinatura no app master; não está sendo possível gerar [checkout/assinatura]. Corrigir e garantir fluxo de ponta a ponta.”

```json
{
  "requires_multiple_agents": true,
  "agents_needed": ["edge-function-specialist", "debugger", "front-end-dev", "migration-validator"],
  "execution_strategy": "sequential",
  "execution_order": [
    {
      "agent": "edge-function-specialist",
      "reason": "Corrigir upsert em subscriptions na asaas-checkout (onConflict) e validar fluxo ASAAS",
      "focus": "supabase/functions/asaas-checkout/index.ts: upsert subscriptions com onConflict: 'user_id'; tratar erros da API ASAAS; garantir que payment (PIX/boleto) seja retornado ao client"
    },
    {
      "agent": "debugger",
      "reason": "Rastrear falhas ponta a ponta: client → Edge Function → ASAAS → DB",
      "focus": "Logs da Edge Function, resposta do invoke no client, verificar secrets e variáveis de ambiente; identificar onde o fluxo quebra (auth, API key, upsert, RLS)"
    },
    {
      "agent": "front-end-dev",
      "reason": "Garantir que o client envia auth e trata erros/resposta do checkout",
      "focus": "apps/client/src/pages/dashboard/Billing.tsx: garantir que session está ativa ao chamar invoke; tratar data.error e data.success; exibir PIX/boleto quando payment vier na resposta"
    },
    {
      "agent": "migration-validator",
      "reason": "Confirmar schema de subscriptions e asaas_* consistente com o código",
      "focus": "Tabelas subscriptions (plan, status, asaas_subscription_id, asaas_customer_id), asaas_customers, asaas_subscriptions; RLS e políticas para user e service_role"
    }
  ],
  "dependencies": {
    "debugger": ["edge-function-specialist"],
    "front-end-dev": ["edge-function-specialist"],
    "migration-validator": []
  },
  "summary": "Problema principal: criação de assinatura/checkout não completa. Sequência: (1) corrigir Edge Function (upsert + erros ASAAS), (2) debugar fluxo completo, (3) ajustar client para auth e UI de pagamento, (4) validar schema e RLS."
}
```

---

## 3. PROMPT PARA O PROMPT-ENGINEER

*(A ser usado pelo @prompt-engineer para gerar os prompts por task em `.cursor/prompts/billing-asaas-tasks.md`.)*

**Contexto:** O checkout ASAAS no app master (página Billing → asaas-checkout) não está conseguindo gerar assinatura/pagamento. Foi feito um rastreamento completo (este documento). O splitter definiu 4 agentes em sequência: edge-function-specialist, debugger, front-end-dev, migration-validator.

**Pedido:** Criar um prompt otimizado por task (um bloco por agente em execution_order), no formato definido na regra master-orchestration FASE 2:

- Metadata: Agent, Files, Dependencies, Duration, Priority  
- Validation Criteria  
- The Prompt: role, mission, context, requirements, arquivos a criar/modificar, critérios de sucesso, formato de saída  
- Opcional: Grind Loop Config (grind_until, max_iterations, validation_command)

Incluir referência a este overview e ao JSON de decomposição para contexto.

---

## 4. INSTRUÇÕES PARA O AGENT-ORCHESTRATOR

**Como coordenar (ler @.cursor/agents/agent-orchestrator.md e @.cursor/rules/master-orchestration.mdc):**

1. **Objetivo:** Coordenar a correção do fluxo de billing/ASAAS (criação de clientes e assinaturas) no app master. O orquestrador **não executa código**; delega aos agentes.
2. **Entradas:**  
   - Este arquivo (overview + decomposição).  
   - `.cursor/prompts/billing-asaas-tasks.md` (já criado) com um prompt por task.
3. **FASE 1:** Já realizada (decomposição JSON na seção 2).  
4. **FASE 2:** Prompts por task já estão em `billing-asaas-tasks.md`. Se quiser refinamento, delegar ao @prompt-engineer com a seção 3 deste doc.  
5. **FASE 3:** Validar com @composer ou @verifier: dependências entre tasks (edge-function → debugger/front-end), consistência dos arquivos listados nos prompts.  
6. **FASE 4:** Para cada task em `execution_order` na seção 2:  
   - Task 1: delegar ao **edge-function-specialist** com o prompt da Task 1 em `billing-asaas-tasks.md`.  
   - Task 2: delegar ao **debugger** com o prompt da Task 2.  
   - Task 3: delegar ao **front-end-dev** com o prompt da Task 3.  
   - Task 4: delegar ao **migration-validator** com o prompt da Task 4.  
   Após cada delegação, usar @verifier para validar critérios da task antes de marcar como concluída.  
7. **FASE 5:** Validação final (testes, build, smoke) e deploy conforme regra master.
8. **Critério de sucesso global:** Usuário logado consegue, na página Billing, clicar em Fazer Upgrade, gerar checkout ASAAS (cliente + assinatura), ver PIX/boleto/fatura quando aplicável, e as tabelas `subscriptions` e asaas_* refletirem o estado correto.

**Nota:** A correção imediata do upsert em `subscriptions` (onConflict: 'user_id') já foi aplicada em `supabase/functions/asaas-checkout/index.ts`. As tasks em `billing-asaas-tasks.md` consolidam essa correção e acrescentam tratamento de erros e validações de schema/front-end.
