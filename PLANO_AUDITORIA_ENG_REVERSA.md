# 🛡️ INNFLOW OS: PLANO MASTER DE AUDITORIA & ENGENHARIA REVERSA

> **Status:** Ativo | **Metodologia:** Análise Estática Cross-Layer (Pente Fino) | **Objetivo:** Zero Falsos Positivos
> **Regra Primária:** Nenhuma execução cega ("Trust me, it works") é aceita. O código fonte (Frontend/Backend) deve refletir 100% da intenção arquitetural do Teste E2E.

## 1. ⚙️ PROTOCOLOS E OTIMIZAÇÕES ATIVAS (ANTIGRAVITY OS)
Durante a execução de cada Módulo, as seguintes leis de sistema operam em plano de fundo:
- **Token Budget (`/token-budget`):** Contexto estrito e Isolamento Módulo a Módulo. Lemos as specs de testes de uma única Macro por vez, mapeamos o real, documentamos e esvaziamos a memória. Limpeza total de contexto antes de ir para a próxima.
- **Lei do Read-Only Puro:** Aqui e Somente Levantamento e Auditoria. O código atual (UI/Banco) será cruzado contra o E2E, produziremos relatórios conclusivos, sem realizar mutações e sem desperdiçar tokens testando na nuvem.
- **Parallel Execution (`/parallel-execution`):** Disparo de Sub-Agentes se um módulo abranger múltiplos repositórios distantes (ex: O front no `apps/client` e o banco em `supabase/migrations`).
- **Context Handoff (`/context-handoff`):** Este documento atua como a Âncora Central. Em caso de quebra ou reinício de sessão, o agente deve ler este arquivo e retomar do último módulo auditado.
- **Grind Mode Auditorial (`/grind`):** Imersão extrema. Mapeia estaticamente qual é o "estado real" (O botão X tem `data-testid` Y? Sim ou Não) e parte para preencher a Dossiê do módulo.

## 2. 🧰 ARSENAL TÁTICO DE AUDITORIA (SKILLS)
Para cada módulo, os Bastidores serão cruzados utilizando:
1.  **Frontend & Interação (`ui-ux-pro-max` / `react-best-practices`):** Verifica renderização condicional do DOM, Modais, coerência dos `data-testid` (ex: `login-email-input`), botões de loading, Zustland stores.
2.  **Mock & Rede (`/validate-integration`):** Interceptação Playwright bate com a chamada HTTP real do Axios ou Fetch? O Payload esperado é igual à resposta da rota?
3.  **Backend & Banco de Dados (`postgres-best-practices` / `/supabase-queries` / `/edge-functions`):** Teste de stress mental das Políticas de RLS, inserções no banco (`auth.users`, `team_invites`) e tratamento `CORS` de Edge Functions.
4.  **Resolução Causal (`systematic-debugging`):** Eliminação do ruído antes que aconteça (ex: Se o Playwright dá *Timeout*, garante-se que a UI foi desenhada para aparecer na tela).


## 3. 🗺️ CRONOGRAMA EXAUSTIVO DE ENGENHARIA REVERSA (564 TESTES DO JULES)

Em vez de rodarmos os testes na nuvem, o Agente lerá cada arquivo `.spec.ts` abaixo, decifrará qual é a exigência funcional estruturada, e mergulhará no nosso código-fonte ("Por baixo do capô") para auditar se o código atende logicamente às 564 exigências que o Jules construiu. Dividimos a auditoria em 7 Macro-Camadas de Validação Causal:

### [ ] Macro 1: O Núcleo de Identidade e Muralha RLS (100+ Testes)
- **Arquivos-Guia:** `auth-complete.spec.ts`, `user-management.spec.ts`, `09-security-roles.spec.ts`, `security.spec.ts`, `security-audit.spec.ts`
- **Engenharia Reversa (Bastidor):** Vasculhar os Fluxos de Auth no Next.js, injeção de JWT pelo `@supabase/supabase-js`, e cruzar com as travas do banco (`Row Level Security` de Inquilinos). Garantir que locators do Playwright como `login-email-input` possuam o hook correto puxando do Estado sem engolir erros silenciosos.

### [ ] Macro 2: Rotas, Navegação e Funil de Setup (80+ Testes)
- **Arquivos-Guia:** `onboarding-flow.spec.ts`, `public-routes-complete.spec.ts`, `dashboard.spec.ts`, `settings-tabs.spec.ts`
- **Engenharia Reversa (Bastidor):** Validar a cadeia de Protected Routes e o Mapeamento da `react-router-dom` (Módulo 1 de Onboarding wizard). Inspecionar o DOM cru das páginas. Os 6 passos exigidos pelo teste E2E do Onboarding existem nativamente na página?

### [ ] Macro 3: O Pulso do CRM e Sistema Realtime (90+ Testes)
- **Arquivos-Guia:** `crm-realtime.spec.ts`, `crm-leads.spec.ts`, `conversations.spec.ts`
- **Engenharia Reversa (Bastidor):** O E2E escuta websockets. Eu farei a reversa: verificarei a implementação do `supabase.channel` nos componentes React, e checarei a arquitetura de cache do React Query (`queryClient.invalidateQueries`). O painel Kanban (`dnd-kit`) suporta as colunas de "Vendido" que o teste tenta arrastar?

### [ ] Macro 4: Fronteira Física: Billing, Limites e Paywalls (70+ Testes)
- **Arquivos-Guia:** `plan-limits-billing.spec.ts`, `billing-subscription.spec.ts`
- **Engenharia Reversa (Bastidor):** Testes rigorosos onde o Jules mockou o disparo da "Asaas". Nossa reversa abrirá os componentes `/plans` e `/dashboard/billing`. O botão "Assinar Plano" está ligado à Edge Function correta (`asaas-checkout`)? Se `limit > max`, a UI exibe o paywall que o teste procura?

### [ ] Macro 5: O Córtex da Inteligência Artificial (90+ Testes)
- **Arquivos-Guia:** `voice-ai-engine.spec.ts`, `ai-knowledge.spec.ts`
- **Engenharia Reversa (Bastidor):** Validação técnica das APIs Nativas. O teste testa Microfone. Nossa auditoria lerá os códigos que acessam `navigator.mediaDevices` ou a lib `AudioContext`. O fluxo de embeddings aponta o form data correto pro PgVector? As páginas contém os Modais de configuração de Voz?

### [ ] Macro 6: Imunidade de Tráfego e Otimização N+1 (80+ Testes)
- **Arquivos-Guia:** `n-plus-one-fixes.spec.ts`, `database-integrity.spec.ts`, `edge-functions.spec.ts`, `automation-triggers.spec.ts`, `integrations.spec.ts`, `bolt-optimizations.spec.ts`, `performance-latency.spec.ts`
- **Engenharia Reversa (Bastidor):** Jules cobrou performance. Nossa reversa vai bater nas consultas Supabase no frontend. Teremos Nested Queries destrutivas (N+1)? Verificaremos se estamos usando `.select('*, organization(*)')` de forma eficiente. E cruzaremos a integridade da tabela de Edge Functions Logs de Gatilhos.

### [ ] Macro 7: O Senado RBAC - Permissões de Equipe (50+ Testes)
- **Arquivos-Guia:** `team-permissions.spec.ts`, `admin-complete.spec.ts`, `dashboard-kpis.spec.ts`
- **Engenharia Reversa (Bastidor):** Como os Botões do Dashboard se comportam se o Reducer tiver `{role: 'agent'}`? Entrarei nos componentes pra rastrear onde os Switchers (ex: Deletar Membro) exigem `role === 'admin'`. O Jules testa essas barreiras exaustivamente.

## Conclusão do Termo Estratégico
Nenhum módulo passa para o estágio "Verde" até o nosso "Testador Inverso" varrer mental e tecnicamente as linhas de código raiz por baixo das peças de interface que o Jules testaria. Uma vez validada a camada, a integridade estrutural local prova cabalmente que a esteira de nuvem não quebra mais. Esculpida sob os padrões **Bolt/Sentinel.**
