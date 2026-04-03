# 🎯 Auditoria e Mapeamento Completo: Sistema Kanban (Pipeline)

Este relatório reflete a auditoria completa realizada nas rotas de Kanban do sistema (foco na versão ativa `KanbanV2` em `/dashboard/kanban`), rastreando desde o roteamento, UX/UI, até o fluxo de dados do CRM e a retroalimentação da Inteligência Artificial.

---

## 1. Rota e Arquitetura Primária
- **Rota Ativa:** O endpoint `/dashboard/kanban` carrega o componente principal `<KanbanV2 />` (acessível apenas por usuários autenticados e que passaram pelo onboarding via `ProtectedRoute` e `OnboardingGuard`).
- **Arquitetura de Dados:** Diferente do modelo legado (que vivia num arquivo antigo `Kanban.tsx`), o atual usa chamadas nativas direto para PostgreSQL buscando da tabela `contacts` (`pipeline_stage_id`) e não apenas do estado transiente de conversas.
- **Roles:** Segue padrão de permissão rigoroso (`useUserRole` e `usePermissions`). Gestores e admins veem toda a equipe; Atendentes ("agents") veem apenas os de `assigned_to = user.id`.

## 2. A Lógica de Integração Dinâmica (CRM ⇄ Kanban)
**"É possível o usuário (atendente ou gestor) durante a conversação já mandar isso direto pro Kanban?"**

**Sim, totalmente integrado e em tempo real.**
- **A Interface nas Conversas:** Dentro da tela de conversas, o sub-componente `ConversationSidebar.tsx` carrega o módulo `<StageSelector />` (chamado nas UI's como seletor visual de pipeline).
- **Ação Rápida:** Sem sair da janela do chat, o gestor/atendente altera a "Etapa do Funil" (Pipeline Stage) em poucos cliques pelo painel lateral do Lead sem complicação.
- **Sync Instantâneo:** Ao mudar no chat, é feito um `UPDATE` transacional no banco em `pipeline_stage_id` dentro da tabela `contacts`. E, o mais vital: o `KanbanV2` conta com um gatilho supersônico ouvindo o evento no `supabase.channel("kanban_contacts")`. Instantaneamente, se houver um gestor ou atendente com o Kanban aberto, a alteração no banco gera atualização real-time via Websockets e o "Cartão/Card" cai ("dá ping") na coluna escolhida na hora. Não é necessário dar refresh (F5).

## 3. O Cruzamento de Dados e o Alimentador de IA

A Inteligência Artificial atua transversalmente, não só no chat, mas no sistema geral. Como isso interage com o Kanban atual?
- **Pipeline Crossover:** A estrutura real `Contact` no BD (usada explicitamente no Kanban) possui as colunas estruturais ativas: `lead_score`, `temperature`, `sentiment_summary` e `intent_summary`. O seu motor Engine V2/Evolution webhook, ao classificar a conversa passivamente (cálculo de sentiment/extract) insere isso direto no contato de forma ubíqua.
- **Como o Kanban Vê:** No Kanban V2 as métricas resumidas `Total`, `Hot`, `Warm` e `Cold` leem estas exatas informações macro geradas pela IA. Eles servem de balizador analítico para os Cartões (exibindo `lead_score` explícito e cores ligando à Temperatura quentura estipulada pela engine termal no contato). A interface do Kanban não é a causadora da IA, ela é o Painel Frontal onde os dados validados de machine learning viram relatórios decisivos!
- **Resumo:** A conversação capta tudo, a IA pontua tudo, e o Kanban orquestra. Ele interage dinamicamente e alimenta todas as entidades simultaneamente enquanto as etapas do pipeline mudam. 

## 4. UI/UX (A Interface "Liquid Glass")
- **Drag & Drop Flúido:** Integra o sistema Pangea DND (`@hello-pangea/dnd`) com `glass-cards` responsivos gerados no seu Design System "Liquid Glass", reativos e com Snapping Horizontal scrollable (Snap-x).
- **Multidimensional Board Mode:** Possui visualizações toggle-able entre "board" (grid tradicional) e "compact" (list mode clean) adaptativo para quando tem excesso de tags e volumes gigantes.
- **Kanban Stats & KPI's:** "Inlines Stats" exibem somatórias completas nas cards superiores, expondo os dados agrupados (pontos ganhos `lead_score_total` + "Total leads" em contagem real).
- **Filtros e Ações de Alto Nível:** Inclui multi-filter real por barra de string, Temperature (Hot, Warm, Cold), Data Range, Origem do Source e Assignee (Agente responsável). Você não fica preso em apenas uma view.

## 5. Rastreabilidade & Histórico (Audit Trails)
Toda vez que o usuário *arrasta/solta* um cartão no Kanban Board, a lógica executa de perto 2 trilhas de dados:
1. **CRM Primary ID:** `supabase.from('contacts').update({ pipeline_stage_id: TARGET_STAGE })` atualizando instantaneamente o local original do funil daquele Lead (Contato).
2. **Histórico Analítico Realtime:** `supabase.from('pipeline_stage_history').insert({ from_stage_id, to_stage_id, user_id })`. Trilha crucial em CRM para poder gerar estatísticas retroativas de LifeCycle Time (Tempo em cada estágio) do Pipeline sem a perda do histórico de alterações do drag/drop.

## 6. Parecer e Veredito da Auditoria
📌 O seu modulo Kanban (V2) **encontra-se num estágio *Premium* (Grade A+) de arquitetura técnica real-time**. Ele respeita 100% as diretrizes `OnboardingGuard`, é Multi-Tenant natural, a conexão (Conversa ⇄ CRM ⇄ Kanban ⇄ Engine IA) compartilha do MESMO dado exato via Supabase RPC + Realtime sockets, garantindo zero des-sincronia, provendo fluidez absurda desde o momento de clique num Chat até o drop de fase do negócio. Não foi encontrado nenhum beco-sem-saída (dead-end).
