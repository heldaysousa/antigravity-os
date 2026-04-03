# Prompts por task – Billing ASAAS (criação de clientes e assinaturas)

Contexto: ver `.cursor/prompts/billing-asaas-rasteramento-overview.md`.

---

## Task 1 – Edge Function asaas-checkout (edge-function-specialist)

**Metadata**
- Agent: edge-function-specialist
- Files: `supabase/functions/asaas-checkout/index.ts`
- Dependencies: nenhuma (primeira na ordem)
- Duration: ~30 min
- Priority: crítica

**Validation Criteria**
- Upsert em `subscriptions` usa `onConflict: 'user_id'` e atualiza plano/status/asaas_subscription_id/asaas_customer_id quando o usuário já tem linha.
- Nova assinatura: insert em `asaas_subscriptions` e upsert em `subscriptions` com onConflict.
- Resposta da função inclui `payment: { invoiceUrl?, bankSlipUrl?, pixQrCode?, pixCopyPaste? }` quando a API ASAAS retornar pagamento.
- Erros da API ASAAS (res não ok) são logados e devolvidos ao client com mensagem clara (sem expor dados sensíveis).

**The Prompt**

```
Você é o responsável pelas Edge Functions Supabase (Deno) do projeto INNFLOW.

Missão: Corrigir e robustecer a Edge Function asaas-checkout para que a criação/atualização de assinaturas e o retorno de dados de pagamento (PIX/boleto) funcionem de forma confiável.

Contexto:
- A tabela `subscriptions` tem UNIQUE(user_id). Hoje o código faz upsert sem onConflict; o PostgREST usa por padrão a primary key (id). Como não enviamos id, o upsert tenta INSERT e falha quando já existe subscription para o user_id.
- Arquivo a modificar: supabase/functions/asaas-checkout/index.ts.

Requisitos:
1. No upsert em `subscriptions`, usar opção onConflict: 'user_id' (e garantir que os campos plan, status, asaas_subscription_id, asaas_customer_id, updated_at sejam atualizados na linha existente).
2. Garantir que, após criar assinatura na ASAAS, o primeiro pagamento seja buscado (GET /payments?subscription=...) e, se billingType for PIX ou o pagamento for PIX, buscar pixQrCode (GET /payments/:id/pixQrCode). Incluir invoiceUrl, bankSlipUrl, pixQrCode, pixCopyPaste no objeto payment da resposta.
3. Em asaasRequest, ao receber !res.ok, logar res.status e data (sem access_token). Retornar ao client uma mensagem de erro genérica mas útil (ex.: "Falha ao comunicar com o gateway de pagamento. Tente novamente ou escolha outra forma de pagamento.").
4. Manter CORS e validação de JWT como estão.

Arquivos a modificar:
- supabase/functions/asaas-checkout/index.ts

Critérios de sucesso:
- Upsert em subscriptions não falha quando o usuário já tem uma linha (atualiza a linha).
- Resposta 200 com body.success true e body.payment preenchido quando a ASAAS retornar pagamento.
- Em caso de erro na API ASAAS, resposta 400 com body.success false e body.error com mensagem adequada.

Formato de saída: Código da Edge Function atualizado; nenhum arquivo de documentação novo.
```

---

## Task 2 – Rastreamento e diagnóstico (debugger)

**Metadata**
- Agent: debugger
- Files: logs Supabase, client (Billing.tsx), env (exemplo)
- Dependencies: Task 1 concluída
- Duration: ~25 min
- Priority: alta

**Validation Criteria**
- Checklist de verificação documentado ou reportado: secrets (ASAAS_API_KEY, ASAAS_API_URL), VITE_SUPABASE_* no client, comportamento do invoke (erro vs data.success false).
- Se possível, um passo a passo para reproduzir o fluxo e onde inspecionar (Network tab, Edge Function logs).

**The Prompt**

```
Você é o responsável por debugging e diagnóstico no projeto INNFLOW.

Missão: Rastrear o fluxo de checkout ASAAS da UI até a Edge Function e a API ASAAS, e identificar onde pode estar falhando quando "não está sendo possível gerar" assinatura/pagamento.

Contexto:
- Fluxo: Billing.tsx → supabase.functions.invoke('asaas-checkout', { body }) → Edge Function asaas-checkout → API ASAAS → writes em asaas_customers, asaas_subscriptions, subscriptions.
- Overview completo: .cursor/prompts/billing-asaas-rasteramento-overview.md.

Requisitos:
1. Listar pontos de falha possíveis: (a) JWT ausente ou expirado no client; (b) Edge Function sem secrets ASAAS_API_KEY / ASAAS_API_URL; (c) upsert subscriptions (já corrigido na Task 1 com onConflict); (d) API ASAAS retornando erro (sandbox vs produção, key inválida); (e) client não tratando data.error ou data.success.
2. Indicar como o desenvolvedor pode verificar: onde ver o retorno de functions.invoke no client (Network ou console); onde ver logs da Edge Function no painel Supabase; que variáveis conferir (Supabase secrets, .env do client com VITE_SUPABASE_URL e VITE_SUPABASE_ANON_KEY ou publishable).
3. Não alterar código de produção; apenas produzir um checklist ou passos de diagnóstico (pode ser em comentário no overview ou em texto direto ao usuário).

Formato de saída: Resposta estruturada com lista de pontos de falha e passos de verificação. Opcional: sugerir uma melhoria pontual no Billing.tsx para logar erro (ex.: console.error) em desenvolvimento.
```

---

## Task 3 – Client Billing (front-end-dev)

**Metadata**
- Agent: front-end-dev
- Files: `apps/client/src/pages/dashboard/Billing.tsx`
- Dependencies: Task 1 concluída
- Duration: ~20 min
- Priority: alta

**Validation Criteria**
- Antes de chamar asaas-checkout, garantir que há sessão (ex.: supabase.auth.getSession() ou user do useAuth); se não houver, mostrar toast e não chamar invoke.
- Tratar explicitamente data?.error (Supabase functions retornam erro em data quando há falha) e data?.success === false.
- Exibir PIX (QR + copiar) e links de boleto/fatura quando paymentResult tiver esses campos.

**The Prompt**

```
Você é o desenvolvedor front-end do app client INNFLOW (React, apps/client).

Missão: Garantir que a página Billing chame o checkout ASAAS com sessão válida e trate erros e a resposta de pagamento (PIX/boleto) corretamente.

Contexto:
- Arquivo: apps/client/src/pages/dashboard/Billing.tsx.
- Já existe handleUpgrade que chama supabase.functions.invoke('asaas-checkout', { body: { plan, billingType } }) e setPaymentResult quando data.payment tem dados.
- useSubscription e useAuth (ou store de auth) estão disponíveis.

Requisitos:
1. Antes de fazer invoke, verificar se existe sessão ativa (ex.: session do useAuth ou supabase.auth.getSession()). Se não existir, exibir toast informando que é preciso estar logado e não chamar a função.
2. Tratar data.error (quando Supabase devolve erro no body) e data.success === false, exibindo toast de erro com data.error ou data.error message.
3. Manter a exibição de paymentResult (PIX QR, copiar código, link boleto, link fatura) quando a resposta trouxer data.payment com esses campos.
4. Não remover ou quebrar o fluxo existente de fetchSubscription após sucesso.

Arquivos a modificar:
- apps/client/src/pages/dashboard/Billing.tsx

Critérios de sucesso:
- Usuário deslogado não dispara invoke e vê mensagem clara.
- Erro retornado pela Edge Function ou pela plataforma aparece em toast.
- Quando o checkout retorna payment com pixQrCode/pixCopyPaste/bankSlipUrl/invoiceUrl, a UI os exibe.
```

---

## Task 4 – Schema e RLS (migration-validator)

**Metadata**
- Agent: migration-validator
- Files: migrations que definem subscriptions, asaas_customers, asaas_subscriptions (e RLS)
- Dependencies: nenhuma (pode rodar em paralelo com Task 1 para validar)
- Duration: ~15 min
- Priority: média

**Validation Criteria**
- subscriptions: existe coluna plan, status, asaas_subscription_id, asaas_customer_id; existe UNIQUE(user_id).
- asaas_customers e asaas_subscriptions existem com RLS que permita ao service_role fazer insert/update; usuário autenticado pode SELECT apenas o próprio registro (por user_id).

**The Prompt**

```
Você é o responsável por validar migrações e schema do banco no projeto INNFLOW (Supabase/Postgres).

Missão: Confirmar que o schema das tabelas de billing (subscriptions, asaas_customers, asaas_subscriptions) e as políticas RLS estão alinhados com o uso na Edge Function asaas-checkout e no client.

Contexto:
- Overview: .cursor/prompts/billing-asaas-rasteramento-overview.md.
- A Edge Function usa service_role para insert/upsert em asaas_customers, asaas_subscriptions e subscriptions. O client lê subscriptions e plan_limits por user_id com usuário autenticado.

Requisitos:
1. Listar migrações relevantes (subscriptions, asaas_*).
2. Verificar: tabela subscriptions tem user_id, plan, status, asaas_subscription_id, asaas_customer_id e constraint UNIQUE(user_id). Confirmar que não há conflito com upsert por user_id (ex.: trigger que exija id).
3. Verificar RLS: subscriptions permite SELECT para auth.uid() = user_id; service_role pode INSERT/UPDATE/DELETE. asaas_customers e asaas_subscriptions: SELECT para user_id = auth.uid(), service_role com ALL.
4. Se encontrar alguma incompatibilidade (ex.: coluna faltando, política bloqueando service_role), reportar e sugerir alteração de migration ou política (sem aplicar em produção sem aprovação).

Formato de saída: Relatório curto (lista de tabelas, colunas relevantes, políticas RLS e conclusão: OK ou itens a corrigir).
```

---

## Uso pelo agent-orchestrator

- Executar as tasks na ordem: 1 → 2, 3 (2 e 3 podem ser em paralelo após 1) → 4 (pode ser em paralelo com 1).
- Após cada task, o verifier valida os critérios da task antes de marcar como concluída.
