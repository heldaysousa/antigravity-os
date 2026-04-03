# 🚀 GUIA DE ORQUESTRAÇÃO AVANÇADA — INNFLOW v2.2

> **Para o Agente (Antigravity):** Este é o seu manual de sistema. Se você estiver perdido, o contexto estiver muito grande, ou se uma task falhou e você não sabe como prosseguir, consulte este documento.

---

## 1. 🥇 REGRA DE OURO DA ORQUESTRAÇÃO

Se você está operando como **Orquestrador**, você é um gerente de projeto. **VOCÊ NÃO ESCREVE CÓDIGO INDIRETAMENTE.**

- **Delegar:** Ao invés de escrever o código no chat, você divide o trabalho, invoca sub-agentes e os coordena.
- **Validar:** Você roda os scripts de build, type-check e testes.
- **Workflow base:** `/master-orchestration`

---

## 2. 🧰 OS 6 FLUXOS DE OTIMIZAÇÃO AVANÇADA (Workflows)

O poder da versão v2.2 vem do uso estratégico dos slash commands. Se você se deparar com uma dor, ative o workflow correspondente:

### 💰 Problema: "Contexto gigante, erro 503, lentidão"

👉 **Solução: Invocar `/token-budget` (`workflows/token-budget.md`)**

- Pare de abrir arquivos desnecessários! Use `grep_search` > `view_file_outline` > `view_file`.
- Só carregue no contexto os arquivos estritamente necessários para a task atual.
- Restrinja o orçamento declarando: _"BUDGET: Só vou ler estes 2 arquivos e mais nada"_.

### 🤝 Problema: "Sessão reiniciou e perdi o contexto"

👉 **Solução: Invocar `/context-handoff` (`workflows/context-handoff.md`)**

- Leia SEMPRE o `.agents/scratchpad.md` antes de enviar o primeiro `git log`. O scratchpad é a _fonte da verdade_.
- Gere o Brieffing de Entrada.
- Ao encerrar a sessão, registre no scratchpad onde parou. O próximo agente continuará dali.

### 🚑 Problema: "O código falhou no build/test"

👉 **Solução: Invocar `/error-recovery` (`workflows/error-recovery.md`)**

- Não devolva a batata pro usuário dizendo: "Erro aqui, conserte".
- **Erro de TypeScript?** Auto-corrija silenciosamente (até 3 tentativas).
- **Erro Estrutural?** Notifique o usuário da mudança de estratégia e tente outra abordagem.
- **Falha Crítica/Catastrófica?** `git stash` -> Documente o erro -> Peça decisão humana.

### ⚡ Problema: "Essa lista de 10 tasks vai demorar horas"

👉 **Solução: Invocar `/parallel-execution` (`workflows/parallel-execution.md`)**

- Identifique tasks que NÃO dependem uma da outra (ex: mudar um componente visual no header e outro no footer).
- Dispare os comandos simultaneamente (Barrier Pattern) no estilo bloco em Paralelo e aguarde todos acabarem para seguir.

### 🔬 Problema: "A task parece pronta, mas será que não quebrou nada?"

👉 **Solução: Invocar `/self-audit` (`workflows/self-audit.md`)**

- Aplique os 5 Filtros Obrigatórios:
  1. O arquivo existe?
  2. O `pnpm run type-check` passou?
  3. Imports resolveram?
  4. Nenhum TODO perigoso deixado para trás?
  5. Layout testado (quando aplicável)?
- Só depois disso marque como `✅ DONE` no scratchpad.

### 🚦 Problema: "Feature perigosa, e se quebrar prod?"

👉 **Solução: Invocar `/feature-flag` (`workflows/feature-flag.md`)**

- Toda funcionalidade instável deve ser coberta por um toggle ON/OFF em variáveis de ambiente.
- Mantenha o código legado disponível até o flag receber aprovação e substituição definitiva.

---

## 3. 🛡️ REGRAS DE CÓDIGO E SEGURANÇA (Projeto Específico)

Essas regras são imutáveis para todo o projeto INNFLOW (estão salvas em `.agents/rules/`):

1. **Segurança de .env (`security-env-rules.md`):** NUNCA, em hipótese alguma, comite arquivos que tenham `.*env*` real com chaves expostas. Mantenha `.env.example` apenas.
2. **React Patterns (`react-rules.md`):** Background da aplicação `#000000` absoluto (Pure Black). O design deve usar Glassmorphism (blur, overlays transparentes de branco e bordas iluminadas). Nada de cores hardcoded!
3. **Edge Functions / Supabase (`edge-functions-rules.md`):** Construídas usando Deno. Sempre responda com CORS configurado (temosa um `cors.ts` compartilhado) e processe/trate erros evitando craschar o endpoint.
4. **Deploy Vercel (`deploy-vercel-rules.md`):** Ordem: Migrations primeiro -> Edge Functions segundo -> Vercel por último. Verifique o status de deploy via CLI/MCP. Se falhar, inverta/fallback antes de ir pra produção.

---

## 4. 🔌 PROTOCOLO MCP (Integrações Externas)

O Cursor usava "dynamic context" e "Agent-Squared". No Antigravity, utilizamos integrações de MCP atráves de chamadas de servidor diretas (ver `.agents/mcp.json`). Quando usar estas ferramentas para coletar dados reais das plataformas no lugar de prever comportamentos:

- **Vercel MCP (`mcp_vercel_*`):** Obrigatório na Fase 5 (após commit). Chame `mcp_vercel_list_deployments` e `mcp_vercel_get_deployment` para garantir que o build da interface (`innflow-master-client` ou similar) atingiu o status `READY`.
- **Supabase MCP:** Para leitura de tabelas cruas, policies do PostgreSQL, e estado do projeto (ao investigar inconsistencias em RLS no fluxo `/debug`).
- **GitHub MCP:** Se você precisar ler pull requests abertos ou realizar checkouts a partir do branch remoto limpo.

> **Importante:** Se você receber falhas de conexão no MCP, os tokens do ambiente não foram injetados. Recue, utilize CLI puro como fallback e notifique o usuário na tela de que as variáveis (`VERCEL_TOKEN`, `SUPABASE_TOKEN`) estão indisponíveis no runtime da IDE.

---

## 5. 🗂️ COMO NAVEGAR NESTA PASTA (`.agents/`)

- `scratchpad.md` 👉 Seu cérebro principal. Onde você anota o andamento (Tasks: X/Y), erros pendentes e o que fazer em seguida. Atualize-o a cada task finalizada!
- `workflows/*.md` 👉 Biblioteca de protocolos e sub-rotinas (como as citadas acimas). Quando necessitar executar alguma automação específica, leia o markdown e aplique.
- `rules/*.md` 👉 Políticas restritivas sobre frameworks e segurança do repo.
- `task-decomposition*.json` 👉 Os mapas JSON da arquitetura de orquestração dividida por agências (front, back, bd, etc). Planejamentos estruturados.

---

**SITUAÇÃO PRESENTE:** Respire fundo, avalie os tokens disponíveis, leia o scratchpad e decida o `next step` baseando-se no `master-orchestration.md`.
