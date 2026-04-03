<![CDATA[<div align="center">

# 🪐 Antigravity OS

### O Sistema Operacional que transforma seu Antigravity de um assistente genérico em um engenheiro de software sênior autônomo.

[![Version](https://img.shields.io/badge/version-4.0-blueviolet?style=for-the-badge)]()
[![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)]()
[![Platform](https://img.shields.io/badge/platform-Gemini_CLI_%7C_Antigravity-black?style=for-the-badge)]()
[![Skills](https://img.shields.io/badge/skills-37+-orange?style=for-the-badge)]()
[![Workflows](https://img.shields.io/badge/workflows-29-blue?style=for-the-badge)]()

**Criado por [Helday Sousa](https://instagram.com/heldaysousa)** · [@heldaysousa](https://instagram.com/heldaysousa)

---

[O que é](#-o-que-é-o-antigravity-os) · [Antes vs Depois](#-antes-vs-depois) · [Instalação](#-instalação) · [Arquitetura](#-arquitetura-do-sistema) · [Skills](#-skills-incluídas) · [Workflows](#-workflows-incluídos) · [Como Funciona](#-como-funciona-na-prática) · [FAQ](#-faq)

</div>

---

## 🧠 O que é o Antigravity OS?

O **Antigravity OS** é um conjunto altamente otimizado de **regras, skills, workflows, protocolos de orquestração e mecanismos de memória** que você instala no seu ambiente Antigravity (Gemini CLI) para transformá-lo de um chatbot que responde perguntas em um **engenheiro de software autônomo de nível sênior**.

Pense assim: o Antigravity "vanilla" (de fábrica) é como um computador novo sem sistema operacional. Ele tem poder bruto (o modelo de linguagem), mas não sabe:

- Como se organizar entre sessões
- Como gerenciar token budget para não explodir contexto
- Como decompor uma tarefa complexa em sub-tarefas paralelas
- Como se auto-auditar antes de dizer "tá pronto"
- Como recuperar de erros sem travar e pedir ajuda desnecessariamente
- Como se adaptar ao hardware onde está rodando

O **Antigravity OS** resolve **todos** esses problemas. É o equivalente a instalar macOS em um hardware Apple — transforma potencial bruto em produtividade real.

### Por que isso existe?

Depois de centenas de horas codando com AI agents, ficou claro que o gargalo não é a inteligência do modelo — é a **falta de estrutura operacional**. O modelo:

- Esquece o contexto entre sessões
- Gasta tokens lendo arquivos inteiros desnecessariamente
- Não sabe priorizar tarefas
- Não se auto-corrige quando algo quebra
- Não documenta o que fez para o próximo agente continuar

O Antigravity OS foi construído **empiricamente** — cada rule, cada workflow, cada skill nasceu de **uma dor real** encontrada durante desenvolvimento de produção.

---

## 📊 Antes vs Depois

| Dimensão | 🔴 Antigravity Vanilla | 🟢 Com Antigravity OS |
|---|---|---|
| **Memória entre sessões** | Zero. Cada sessão começa do zero | `STATE.md` + `HANDOFF.md` + Knowledge DB persistem contexto |
| **Gestão de tokens** | Sem controle. Lê arquivos inteiros de 5000 linhas | Lei dos 3.300 tokens + LSFS (Lazy Smart File Search) |
| **Decomposição de tarefas** | Tenta fazer tudo de uma vez e trava | `/decompose-task` quebra em sub-tasks com dependências |
| **Recuperação de erros** | Para e pergunta "o que faço?" | `/error-recovery` — 3 níveis de auto-correção silenciosa |
| **Paralelismo** | Sequencial sempre | `/parallel-execution` — sub-agentes simultâneos |
| **Auto-auditoria** | Diz "pronto" sem verificar | `/self-audit` — 5 filtros obrigatórios antes de ✅ |
| **Qualidade de código** | Genérica | 37+ skills especializadas (TypeScript, React, Postgres, Security) |
| **Orquestração** | Inexistente | Master Orchestrator com delegation pattern |
| **Deploy** | Manual | `/deploy-safe` — pipeline com rollback automático |
| **Hardware awareness** | Ignora limites | Adapta paralelismo ao hardware real (RAM/CPU checks) |
| **Segurança** | Best-effort | Skills dedicadas (RLS, OWASP, secrets, .env isolation) |
| **Feature flags** | Inexistente | `/feature-flag` — toggle ON/OFF sem deploy separado |

---

## 🔧 Instalação

### Pré-requisitos

- [Gemini CLI](https://github.com/google-gemini/gemini-cli) instalado e funcionando
- Um workspace/projeto onde você quer ativar o OS
- Git instalado

### Passo 1: Clone o Repositório

```bash
git clone https://github.com/heldaysousa/antigravity-os.git
```

### Passo 2: Copie a pasta `.agents/` para a raiz do seu projeto

```bash
# Navegue até o seu projeto
cd /caminho/do/seu/projeto

# Copie toda a estrutura do OS para seu workspace
cp -r /caminho/para/antigravity-os/.agents/ ./.agents/
```

### Passo 3: (Opcional) Copie os trackers de estado

Se você quer o sistema de memória entre sessões:

```bash
# Copia os arquivos de tracking contextual
cp /caminho/para/antigravity-os/STATE.md ./STATE.md
cp /caminho/para/antigravity-os/HANDOFF.md ./HANDOFF.md
cp /caminho/para/antigravity-os/GUIAMESTRE.md ./GUIAMESTRE.md
```

> ⚠️ **Importante:** Edite o `STATE.md` para refletir o estado real do **seu** projeto. O arquivo que vem no repo é um template — limpe o conteúdo específico e mantenha apenas a estrutura.

### Passo 4: Configure as regras globais (Opcional — Nível Avançado)

Para ativar as regras globais do OS em **todos** os seus projetos:

```bash
# Crie o diretório do bootstrap global
mkdir -p ~/.antigravity/

# Crie o arquivo de bootstrap
cat > ~/.antigravity/AGENT_BOOTSTRAP.md << 'EOF'
# ANTIGRAVITY OS — BOOTSTRAP v4.0

## 1. LIMITES DE TOKENS (LEI DOS 3.300 TOKENS)
- AGENT_BOOTSTRAP: ~500t | STATE.md: ~300t | Spec: ~200t | Skill JIT: ~800t | LSFS: ~1.500t
- Tarefa > 3.300 tokens → QUEBRAR EM FASES obrigatoriamente.

## 2. LSFS (OBRIGATÓRIO)
- NUNCA: cat / ler arquivos inteiros que passarem do budget.
- SEMPRE: usar ferramentas de busca (codebase-search, grep_search) para focar no código relevante.

## 3. ERROR RECOVERY
1. Consulta rápida à base de conhecimento local.
2. Auto-correção silenciosa tentando estratégia diferente.
3. Se o erro persistir: Notificar obstáculo e aguardar decisão humana.
EOF
```

### Passo 5: Configure o `gemini.json` do seu projeto (Recomendado)

Adicione no seu `gemini.json` (ou no settings global `~/.gemini/settings.json`):

```json
{
  "userRules": [
    "# ANTIGRAVITY OS v4.0",
    "## LIMITES DE TOKENS (LEI DOS 3.300)",
    "- AGENT_BOOTSTRAP: ~500t | STATE.md: ~300t | Spec: ~200t | Skill JIT: ~800t | LSFS: ~1.500t",
    "- Tarefa > 3.300 tokens → QUEBRAR EM FASES obrigatoriamente.",
    "",
    "## LSFS (OBRIGATÓRIO)",
    "- NUNCA: cat / ler arquivos inteiros que excedam o budget.",
    "- SEMPRE: usar grep_search / codebase-search antes de view_file.",
    "",
    "## ERROR RECOVERY",
    "1. Auto-correção silenciosa (até 3 tentativas).",
    "2. Estratégia alternativa se primeira falhou.",
    "3. Notificar humano apenas se tudo falhar."
  ]
}
```

### Pronto! 🎉

A partir de agora, quando você abrir o Gemini CLI no seu projeto, o agente vai:
1. Detectar automaticamente a pasta `.agents/`
2. Carregar os skills e workflows disponíveis
3. Seguir os protocolos do Antigravity OS
4. Operar como um engenheiro sênior com memória, auto-correção e paralelismo

---

## 🏗️ Arquitetura do Sistema

```
seu-projeto/
├── .agents/                          # ← CORE DO ANTIGRAVITY OS
│   ├── rules/                        # Regras estritas que o agente SEMPRE segue
│   │   ├── core-rules.md             # Lei dos tokens, MCP policy, modelo
│   │   └── no-browser.md             # Restrições de acesso a browser
│   │
│   ├── skills/                       # 37+ habilidades especializadas (JIT loaded)
│   │   ├── debugger/                 # Debug sistemático com estado persistente
│   │   ├── executor/                 # Commit atômico + checkpoint protocols
│   │   ├── planner/                  # Decomposição tipo "tree of thoughts"
│   │   ├── plan-checker/             # Validação de planos antes de executar
│   │   ├── verifier/                 # Verificação empírica de trabalho feito
│   │   ├── empirical-validation/     # Exige prova antes de marcar como "done"
│   │   ├── token-budget/             # Gerenciamento de budget de tokens
│   │   ├── typescript-pro/           # TypeScript avançado com tipos estritos
│   │   ├── react-best-practices/     # 45+ regras de performance React/Next.js
│   │   ├── postgres-best-practices/  # 32+ regras de otimização Postgres
│   │   ├── backend-security-coder/   # Segurança backend (OWASP, RLS, auth)
│   │   ├── auth-implementation-patterns/ # Padrões de autenticação
│   │   ├── ui-ux-pro-max/            # 50 estilos, 21 paletas, 50 font pairings
│   │   ├── zod-validation-expert/    # Validação Zod + React Hook Form + tRPC
│   │   ├── zustand-store-ts/         # Stores Zustand tipados
│   │   ├── vercel-deployment/        # Deploy em Vercel com Next.js
│   │   ├── vercel-ai-sdk-expert/     # Vercel AI SDK (useChat, streaming, tools)
│   │   ├── gemini-api-dev/           # Google Gemini API integration
│   │   ├── llm-ops/                  # RAG, embeddings, fine-tuning, evals
│   │   ├── git-hooks-automation/     # Husky, lint-staged, commitlint
│   │   ├── edge-functions/           # Edge Functions (Deno, Supabase)
│   │   ├── self-improving-agent/     # Auto-evolução do agente
│   │   └── ...                       # E muito mais
│   │
│   ├── workflows/                    # 29 processos passo-a-passo (slash commands)
│   │   ├── master-orchestration.md   # 🎯 Orquestrador Mestre (multi-agente)
│   │   ├── error-recovery.md         # 🚑 Auto-recuperação de erros
│   │   ├── parallel-execution.md     # ⚡ Execução paralela de sub-agentes
│   │   ├── token-budget.md           # 💰 Economia de tokens
│   │   ├── context-handoff.md        # 🤝 Transferência de contexto entre sessões
│   │   ├── self-audit.md             # 🔬 Auto-auditoria antes de finalizar
│   │   ├── feature-flag.md           # 🚦 Feature flags para deploys seguros
│   │   ├── decompose-task.md         # 🧩 Decomposição de tasks complexas
│   │   ├── plan.md                   # 📋 Plan Mode (dry-run antes de codar)
│   │   ├── grind.md                  # 🔄 Loop de correção automática
│   │   ├── debug.md                  # 🐛 Debug e troubleshooting
│   │   ├── code-review.md            # 👀 Code review de segurança e qualidade
│   │   ├── security.md               # 🔐 Regras de segurança
│   │   ├── testing.md                # 🧪 Padrões de testes
│   │   ├── deploy-safe.md            # 🚀 Deploy seguro com rollback
│   │   ├── migrations.md             # 🗄️ Migrations com RLS e rollback
│   │   ├── create-component.md       # 🧱 Criar componente seguindo padrões
│   │   ├── validate-integration.md   # ✅ Validação cross-layer
│   │   └── ...                       # E mais 10
│   │
│   ├── prompts/                      # Templates de prompt reutilizáveis
│   └── scratchpad.md                 # Memória de curto prazo do agente
│
├── STATE.md                          # 📊 Estado atual do projeto (boot ~300 tokens)
├── HANDOFF.md                        # 🤝 Briefing para transferência entre sessões
└── GUIAMESTRE.md                     # 📖 Manual do sistema para o agente (~400 tokens)
```

---

## 🎯 Skills Incluídas

O Antigravity OS vem com **37+ skills** que são carregadas **Just-In-Time** (JIT) — o agente só carrega a skill quando precisa, economizando tokens:

### 🔧 Core (Operação do Agente)
| Skill | Descrição |
|---|---|
| `executor` | Executa planos com commits atômicos, desvio handling e checkpoints |
| `planner` | Cria planos em fases com dependências e verificação goal-backward |
| `plan-checker` | Valida planos antes de executar para pegar problemas cedo |
| `debugger` | Debug sistemático com estado persistente e contexto fresco |
| `verifier` | Valida trabalho implementado contra spec com evidência empírica |
| `empirical-validation` | Exige prova real antes de marcar como completo |
| `token-budget` | Estima e rastreia budget de tokens para prevenir overflow |
| `self-improving-agent` | Auto-evolução: o agente melhora seus próprios processos |
| `intake` | Brainstorming: transforma ideias vagas em designs validados |

### 💻 Desenvolvimento
| Skill | Descrição |
|---|---|
| `typescript-pro` | TypeScript avançado: generics, strict mode, decorators, enterprise patterns |
| `react-best-practices` | 45+ regras de performance para React e Next.js (mantidas pela Vercel) |
| `zustand-store-ts` | Stores Zustand tipados com middleware e padrões estabelecidos |
| `zod-validation-expert` | Validação Zod com React Hook Form, Next.js e tRPC |
| `create-component` | Cria componentes React seguindo padrões: functional, TS strict, acessíveis |

### 🗄️ Backend & Database
| Skill | Descrição |
|---|---|
| `postgres-best-practices` | 32+ regras de otimização Postgres: índices, queries, locks, monitoring |
| `edge-functions` | Desenvolver Edge Functions com Deno (CORS, cold start, error handling) |
| `migrations` | Migrations seguras com RLS, multi-tenant, rollback |
| `backend-security-coder` | Segurança backend: input validation, auth, API security (OWASP) |
| `auth-implementation-patterns` | Padrões de autenticação e autorização enterprise-grade |

### 🎨 UI/UX & Design
| Skill | Descrição |
|---|---|
| `ui-ux-pro-max` | 50 estilos visuais, 21 paletas, 50 font pairings, 20 charts, 9 stacks |

### 🚀 Deploy & DevOps
| Skill | Descrição |
|---|---|
| `vercel-deployment` | Deploy em Vercel com Next.js: configuração, domínios, edge config |
| `vercel-automation` | Automação via MCP: deployments, DNS, env vars, projects |
| `vercel-ai-sdk-expert` | Vercel AI SDK: useChat, streaming, function calling |
| `deploy-safe` | Deploy seguro com verificação e rollback automático |
| `git-hooks-automation` | Husky, lint-staged, commitlint: qualidade antes do CI |
| `git-workflow` | Workflow Git estruturado para equipes |

### 🤖 AI & LLM
| Skill | Descrição |
|---|---|
| `gemini-api-dev` | Google Gemini API: multimodal, streaming, function calling |
| `gemini-api-integration` | Integração Gemini em projetos reais de produção |
| `llm-ops` | RAG, embeddings, vector DB, fine-tuning, prompt engineering, evals |
| `rag` | Retrieval Augmented Generation: implementação e otimização |

---

## 🔄 Workflows Incluídos

Os workflows são **processos passo-a-passo** ativados via slash commands. São o coração operacional do OS:

### 🎯 Orquestração & Planejamento
| Comando | Para que serve |
|---|---|
| `/master-orchestration` | Orquestração multi-agente para implementações complexas (>10 tasks) |
| `/decompose-task` | Decomposição analítica de tasks complexas em sub-tasks com dependências |
| `/parallel-execution` | Dispara múltiplos sub-agentes simultaneamente (reduz tempo 60-70%) |
| `/plan` | Força o agente a pensar primeiro e pedir aprovação antes de codar |

### 🔧 Desenvolvimento & Debug
| Comando | Para que serve |
|---|---|
| `/grind` | Loop de correção automática até o código passar sem erros |
| `/debug` | Análise de causa raiz, instrumentação, diagnóstico |
| `/code-review` | Code review de segurança e qualidade |
| `/create-component` | Criar componente seguindo padrões do projeto |

### 🧠 Contexto & Memória
| Comando | Para que serve |
|---|---|
| `/context-handoff` | Transferência de contexto entre sessões sem re-explicar tudo |
| `/read-project-state` | Leitura real-time do estado do projeto  |
| `/token-budget` | Economia de tokens quando contexto está grande |

### 🚀 Deploy & Segurança
| Comando | Para que serve |
|---|---|
| `/deploy-safe` | Pipeline de deploy seguro com verificação e rollback |
| `/security` | Regras de segurança: .env, secrets, RLS, OWASP |
| `/feature-flag` | Toggle ON/OFF para features de risco |
| `/testing` | Padrões de testes: Vitest, pgTAP, Playwright |
| `/migrations` | Migrations Supabase com RLS, multi-tenant, rollback |

### 🔬 Qualidade & Validação
| Comando | Para que serve |
|---|---|
| `/self-audit` | Checklist obrigatório antes de marcar task como concluída |
| `/validate-integration` | Validação cross-layer: frontend ↔ backend ↔ database ↔ deploy |
| `/error-recovery` | Recuperação autônoma de erros: retry, escalação, rollback |

---

## ⚙️ Como Funciona na Prática

### 1. A Lei dos 3.300 Tokens

O princípio fundamental do OS. O agente opera com um **budget fixo de ~3.300 tokens de contexto** para evitar degradação de qualidade:

```
AGENT_BOOTSTRAP:    ~500 tokens  (regras do sistema)
STATE.md:           ~300 tokens  (estado atual do projeto)
Spec da tarefa:     ~200 tokens  (o que precisa ser feito)
Skill carregada:    ~800 tokens  (habilidade JIT para a tarefa)
LSFS (busca):     ~1.500 tokens  (código encontrado via grep/search)
─────────────────────────────────
TOTAL:            ~3.300 tokens
```

Se o agente precisa de mais contexto, ele **quebra a tarefa em fases** ao invés de sobrecarregar a janela.

### 2. LSFS — Lazy Smart File Search

O agente **nunca** lê arquivos inteiros. Ele:
1. Usa `grep_search` para encontrar exatamente as linhas relevantes
2. Usa `view_file_outline` para entender a estrutura
3. Só então usa `view_file` com ranges específicas

Isso economiza **70-80% dos tokens** em projetos grandes.

### 3. Memória entre Sessões

```
Sessão 1: "Implementei login e estou no passo 3 de 5. Falta logout e reset."
         → Salva em STATE.md + HANDOFF.md

Sessão 2: Agente lê STATE.md no boot → continua exatamente de onde parou
         → "Vejo que login está pronto. Vou começar pelo logout."
```

### 4. Error Recovery em 3 Níveis

```
Nível 1: Erro leve (typo, import errado)
         → Auto-correção silenciosa

Nível 2: Erro estrutural (abordagem errada)
         → Notifica mudança de estratégia, tenta alternativa

Nível 3: Falha crítica (tudo quebrou)
         → git stash → documenta erro → pede decisão humana
```

### 5. Orquestração Multi-Agente

Para tarefas complexas (>10 sub-tasks), o agente ativa o **Master Orchestrator:**

```
Você (humano):  "Implementa autenticação completa"
                        ↓
Orquestrador:   Decompõe em 8 sub-tasks
                        ↓
                Tasks 1,2,3 (independentes) → executa em PARALELO
                        ↓
                Task 4 (depende de 1) → aguarda, depois executa
                        ↓
                Self-audit de cada task → só marca ✅ se passou nos 5 filtros
                        ↓
                Validate-integration → verifica tudo junto
                        ↓
                HANDOFF atualizado para próxima sessão
```

---

## 🎛️ Configuração Avançada

### Adaptando ao seu Hardware

O OS foi projetado para rodar em hardware modesto. Edite as regras conforme seu setup:

```markdown
# Em .agents/rules/core-rules.md

# Para máquinas modestas (8GB RAM):
Máx 2 sub-agentes paralelos. Serializar se CPU > 80% ou RAM > 6GB.

# Para máquinas potentes (16GB+ RAM):
Máx 4 sub-agentes paralelos. Serializar se CPU > 90% ou RAM > 12GB.
```

### Adicionando Skills Customizadas

```bash
# Crie uma nova skill
mkdir -p .agents/skills/minha-skill/

# Crie o arquivo principal
cat > .agents/skills/minha-skill/SKILL.md << 'EOF'
---
name: Minha Skill Custom
description: Descreva o que ela faz
---

# Instruções para o Agente
[Suas regras e padrões aqui]
EOF
```

### Adicionando Workflows Customizados

```bash
cat > .agents/workflows/meu-workflow.md << 'EOF'
---
description: Descrição do que esse workflow faz
---

## Passos

1. Primeiro passo
2. Segundo passo
3. Terceiro passo
EOF
```

Para ativar: digite `/meu-workflow` no Gemini CLI.

---

## 🎓 FAQ

### "Funciona com qualquer projeto?"
**Sim.** O OS é agnóstico de framework/linguagem. As skills cobrem React, Next.js, Vue, Svelte, Flutter, React Native, PostgreSQL, Deno, e mais. Ele se adapta ao seu projeto.

### "Preciso saber programar para usar?"
Sim, é uma ferramenta para desenvolvedores. O OS potencializa o agente, mas você precisa entender o que ele está fazendo pra direcionar bem.

### "Funciona com o Gemini CLI gratuito?"
**Sim.** Foi projetado para funcionar com a versão gratuita, respeitando limites de tokens e rate limits.

### "Posso modificar as skills e workflows?"
**Absolutamente.** Esse é o ponto. Fork, customize, adicione skills do seu domínio. Quanto mais personalizado, melhor o agente opera.

### "Isso substitui o Cursor / Windsurf / Copilot?"
Não é concorrente — é **complementar** ou uma alternativa. O Antigravity OS foca em transformar o Gemini CLI em um agente autônomo com memória e auto-correção, algo que IDEs com AI não fazem nesse nível.

### "Posso contribuir?"
Sim! Fork, melhore, e abra PRs. O ecossistema cresce quando a comunidade colabora.

---

## 📈 Números Reais (Benchmarks)

Métricas observadas em produção real durante 3+ meses de uso intensivo:

| Métrica | Sem OS | Com OS | Melhoria |
|---|---|---|---|
| Tokens gastos por sessão | ~50k | ~12k | **-76%** |
| Tasks completadas sem intervenção | ~30% | ~85% | **+183%** |
| Erros não recuperados | ~40% | ~5% | **-87.5%** |
| Context loss entre sessões | 100% | ~5% | **-95%** |
| Tempo para tarefas complexas (10+ steps) | 3-4h | 45min-1h | **-75%** |

---

## 🤝 Créditos & Contato

<div align="center">

### Criado por **Helday Sousa**

[![Instagram](https://img.shields.io/badge/Instagram-@heldaysousa-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://instagram.com/heldaysousa)

O Antigravity OS nasceu de **centenas de horas** de desenvolvimento real com AI agents. Cada otimização, cada workflow, cada skill veio de uma dor real encontrada em produção.

Se esse projeto te ajudou, dá uma estrela ⭐ e me segue no Instagram para mais conteúdo sobre AI-powered development.

**Instagram:** [@heldaysousa](https://instagram.com/heldaysousa)

</div>

---

## 📜 Licença

MIT — Use, modifique, distribua. Só dá os créditos.

---

<div align="center">

**🪐 O futuro não é sobre a AI substituir o dev. É sobre o dev ter uma AI que sabe trabalhar direito.**

*Antigravity OS v4.0 — por [Helday Sousa](https://instagram.com/heldaysousa)*

</div>
]]>
