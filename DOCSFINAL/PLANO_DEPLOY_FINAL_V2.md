# ⚡ PLANO DE DEPLOY & VERIFICAÇÃO FINAL (INNFLOW V2)

**Protocolos Invocados:** `/deploy-safe`, `/self-audit`, `/testing`
**Abordagem:** Antigravity OS GSD Flow (Plan → Verify → Execute)

---

## 🎯 SPEC: OBJETIVO
Compilar todas as camadas da plataforma com a prova real dos fixs injetados, efetuando o lançamento na nuvem (Supabase + Vercel) mantendo a ordem estrita obrigatória do ecossistema INNFLOW. Garantir integridade funcional completa perante clientes SaaS.

## 📁 ARQUIVOS E ESCOPO CENTRAL
- Base Frontend Completa (`apps/client/`)
- Modificações de Banco `supabase/functions/trigger-scheduler/index.ts` (Aprimoramento Promisse.all / Chunk Batch).
- Asserções de Qualidade Playwright (`tests/e2e/*`).

## 🚧 RESTRIÇÕES INTEGRADAS (HARD RULES)
1. **Ordem de Subida Engessada:** Nenhuma tela da Vercel sobe antes que o schema do Banco (DB) e as Edge Functions do motor em nuvem estejam compiladas no Supabase.
2. **Tolerância Zero para Erro de Sintaxe:** Deploy bloqueado na Fase 1 caso o `type-check` aponte tipos falhos gerados nos scripts.
3. **Commit Atômico Limpo:** Mensagens seguindo padrão SemVer Semântico (ex: `feat(core): innflow v2`).

---

## 🔍 FASE 1: VERIFY (Checklist Local Pré-Voo)
O Agente rodará (se acionado) a malha de segurança abaixo dentro do motor `pnpm` do seu VS-Code para atestar que os refactors não quebraram referências de rota:

1. **Lint & Memory TS:** `pnpm run type-check` (Assegura que nenhum componente React está clamando variáveis Inexistentes como herança do código do Jules).
2. **Smoke Test Principal:** `pnpm exec playwright test tests/e2e/client/auth.spec.ts` (Testar login raiz simulado na base limpa).
3. **Rollup Bundle Test:** Executar `pnpm run build` na aba Client para flagrar potenciais "White Screens" originadas de Imports perdidos.

---

## 🚀 FASE 2: EXECUTE (/deploy-safe INNFLOW)

### Passo 2.1: Nuvem Traseira (Supabase)
Após atestar build verde, enviamos a carga bruta de conectividade.
```bash
# Sincronização e envio de possíveis novas Roles/RLSs configuradas nas sessões passadas.
supabase db diff
supabase db push

# Re-deploy das funções refatoradas sem limite de time-out
supabase functions deploy trigger-scheduler
supabase functions deploy evolution-instance-manager
supabase functions deploy asaas-checkout
```

### Passo 2.2: Nuvem Frontal (Interface Vercel)
O código principal (Client) com as chancelas sanitizadas (DOMPurify/XSS) levanta voo atrelado à Vercel CLI.
```bash
# Envio para Preview Branch para conferência E2E online
vercel --preview

# Subida Final de Produção após confirmação no domínio
vercel --prod
```

---

## 🛡️ FASE 3: MONITORAMENTO GSD (Post-Mortem / Pós-Voo)
- Testar Endpoint do Edge via Painel do Supabase com Payload Mock verificando a banda de rede (`< 500ms`).
- Operar abertura de um contato via Kanban no painel Live verificando latências de WebSocket (Se as notificações sobem e travam o Drag&Drop real).
- Acompanhamento 10 min de Logs via `vercel logs`.

---

## 🏁 CRITÉRIO DE ENCERRAMENTO (SUCCESS METRIC)
O painel em produção da InnFlow responde via HTTPS na Vercel, o Edge Supabase aciona triggers sem estouro de Connection Pool de rede (N+1 resolvido), as telas compiladas atestam "Zero Downtime", e a base SaaS passa a receber clientes de mercado livre de bugs do Jules.
