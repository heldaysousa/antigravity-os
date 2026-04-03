<p align="center">
  <img src="https://img.shields.io/badge/version-4.0-blueviolet?style=for-the-badge" alt="Version" />
  <img src="https://img.shields.io/badge/license-MIT-green?style=for-the-badge" alt="License" />
  <img src="https://img.shields.io/badge/platform-Gemini_CLI-black?style=for-the-badge" alt="Platform" />
  <img src="https://img.shields.io/badge/skills-29-orange?style=for-the-badge" alt="Skills" />
  <img src="https://img.shields.io/badge/workflows-28-blue?style=for-the-badge" alt="Workflows" />
</p>

<h1 align="center">🪐 Antigravity OS</h1>

<p align="center">
  <strong>O Sistema Operacional que transforma o Gemini CLI<br/>de um assistente genérico em um engenheiro de software sênior autônomo.</strong>
</p>

<p align="center">
  Criado por <strong><a href="https://instagram.com/heldaysousa">Helday Sousa</a></strong> · <a href="https://instagram.com/heldaysousa">@heldaysousa</a>
</p>

<p align="center">
  <a href="#-o-que-é">O que é</a> •
  <a href="#-antes-vs-depois">Antes vs Depois</a> •
  <a href="#-instalação">Instalação</a> •
  <a href="#-arquitetura">Arquitetura</a> •
  <a href="#-skills">Skills</a> •
  <a href="#-workflows">Workflows</a> •
  <a href="#-como-funciona">Como Funciona</a> •
  <a href="#-faq">FAQ</a>
</p>

---

<br/>

## 🧠 O que é

O **Antigravity OS** é um conjunto otimizado de **regras, skills, workflows, protocolos de orquestração e mecanismos de memória** que você instala no Gemini CLI para transformá-lo de um chatbot que responde perguntas em um **engenheiro de software autônomo de nível sênior**.

O Antigravity "vanilla" (de fábrica) é como um computador sem sistema operacional. Ele tem poder bruto (o modelo de linguagem), mas não sabe:

- 🧠 Se organizar entre sessões
- 📊 Gerenciar token budget para não explodir contexto
- 🧩 Decompor uma tarefa complexa em sub-tarefas paralelas
- 🔍 Se auto-auditar antes de dizer "tá pronto"
- 🚑 Recuperar de erros sem travar e pedir ajuda
- 💻 Se adaptar ao hardware onde está rodando

> **O Antigravity OS resolve todos esses problemas.** É o equivalente a instalar macOS em um hardware Apple — transforma potencial bruto em produtividade real.

<br/>

### Por que isso existe?

Depois de centenas de horas codando com AI agents, ficou claro que o gargalo não é a inteligência do modelo — é a **falta de estrutura operacional**.

O modelo esquece contexto entre sessões, gasta tokens lendo arquivos inteiros, não prioriza tarefas, não se auto-corrige, e não documenta o que fez para o próximo agente continuar.

O Antigravity OS foi construído **empiricamente** — cada rule, cada workflow, cada skill nasceu de uma dor real encontrada durante desenvolvimento de produção.

<br/>

---

<br/>

## 📊 Antes vs Depois

| Dimensão | 🔴 Vanilla | 🟢 Com Antigravity OS |
|:---|:---|:---|
| **Memória entre sessões** | Zero — cada sessão do zero | `STATE.md` + `HANDOFF.md` persistem contexto |
| **Gestão de tokens** | Sem controle — lê arquivos de 5000 linhas | Lei dos 3.300 tokens + LSFS |
| **Decomposição de tarefas** | Tenta tudo de uma vez | `/decompose-task` com dependências |
| **Recuperação de erros** | Para e pergunta "o que faço?" | 3 níveis de auto-correção silenciosa |
| **Paralelismo** | Sequencial sempre | Sub-agentes simultâneos |
| **Auto-auditoria** | Diz "pronto" sem verificar | 5 filtros obrigatórios antes de ✅ |
| **Qualidade de código** | Genérica | 29 skills especializadas |
| **Orquestração** | Inexistente | Master Orchestrator com delegation |
| **Deploy** | Manual | Pipeline com rollback automático |
| **Hardware awareness** | Ignora limites | Adapta paralelismo à RAM/CPU |
| **Segurança** | Best-effort | RLS, OWASP, secrets, .env isolation |
| **Feature flags** | Inexistente | Toggle ON/OFF sem deploy |

<br/>

---

<br/>

## 🔧 Instalação

### Pré-requisitos

- ✅ [Gemini CLI](https://github.com/google-gemini/gemini-cli) instalado e funcionando
- ✅ Um workspace/projeto onde você quer ativar o OS
- ✅ Git instalado

<br/>

### Passo 1 — Clone o repositório

```bash
git clone https://github.com/heldaysousa/antigravity-os.git
```

<br/>

### Passo 2 — Copie `.agents/` para a raiz do seu projeto

```bash
cd /caminho/do/seu/projeto
cp -r /caminho/para/antigravity-os/.agents/ ./.agents/
```

<br/>

### Passo 3 — Copie os trackers de estado (opcional)

Se você quer o sistema de memória entre sessões:

```bash
cp /caminho/para/antigravity-os/STATE.md ./STATE.md
cp /caminho/para/antigravity-os/HANDOFF.md ./HANDOFF.md
cp /caminho/para/antigravity-os/GUIAMESTRE.md ./GUIAMESTRE.md
```

> ⚠️ Edite o `STATE.md` para refletir o estado real do seu projeto. O arquivo que vem é um template.

<br/>

### Passo 4 — Configure regras globais (avançado, opcional)

Para ativar as regras em **todos** os seus projetos:

```bash
mkdir -p ~/.antigravity/

cat > ~/.antigravity/AGENT_BOOTSTRAP.md << 'EOF'
# ANTIGRAVITY OS — BOOTSTRAP v4.0

## 1. LIMITES DE TOKENS (LEI DOS 3.300 TOKENS)
- AGENT_BOOTSTRAP: ~500t | STATE.md: ~300t | Spec: ~200t | Skill JIT: ~800t | LSFS: ~1.500t
- Tarefa > 3.300 tokens → QUEBRAR EM FASES obrigatoriamente.

## 2. LSFS (OBRIGATÓRIO)
- NUNCA: cat / ler arquivos inteiros que passarem do budget.
- SEMPRE: usar ferramentas de busca para focar no código relevante.

## 3. ERROR RECOVERY
1. Consulta rápida à base de conhecimento local.
2. Auto-correção silenciosa tentando estratégia diferente.
3. Se o erro persistir: Notificar obstáculo e aguardar decisão humana.
EOF
```

<br/>

### Passo 5 — Configure o `gemini.json` (recomendado)

Adicione no `gemini.json` do projeto ou no global `~/.gemini/settings.json`:

```json
{
  "userRules": [
    "# ANTIGRAVITY OS v4.0",
    "## LIMITES DE TOKENS (LEI DOS 3.300)",
    "- AGENT_BOOTSTRAP: ~500t | STATE.md: ~300t | Spec: ~200t | Skill JIT: ~800t | LSFS: ~1.500t",
    "- Tarefa > 3.300 tokens → QUEBRAR EM FASES obrigatoriamente.",
    "",
    "## LSFS (OBRIGATÓRIO)",
    "- NUNCA: ler arquivos inteiros que excedam o budget.",
    "- SEMPRE: usar grep_search / codebase-search antes de view_file.",
    "",
    "## ERROR RECOVERY",
    "1. Auto-correção silenciosa (até 3 tentativas).",
    "2. Estratégia alternativa se primeira falhou.",
    "3. Notificar humano apenas se tudo falhar."
  ]
}
```

<br/>

### ✅ Pronto!

A partir de agora, quando você abrir o Gemini CLI no seu projeto, o agente vai:

1. Detectar automaticamente a pasta `.agents/`
2. Carregar os skills e workflows disponíveis
3. Seguir os protocolos do Antigravity OS
4. Operar como um engenheiro sênior com memória, auto-correção e paralelismo

<br/>

---

<br/>

## 🏗️ Arquitetura

```
seu-projeto/
│
├── .agents/                              ← CORE DO ANTIGRAVITY OS
│   │
│   ├── rules/                            Regras estritas (sempre ativas)
│   │   ├── core-rules.md                 Lei dos tokens, MCP policy
│   │   └── no-browser.md                 Restrições de browser
│   │
│   ├── skills/                           29 skills especializadas (JIT)
│   │   ├── debugger/                     Debug sistemático
│   │   ├── executor/                     Commits atômicos + checkpoints
│   │   ├── planner/                      Decomposição "tree of thoughts"
│   │   ├── typescript-pro/               TypeScript avançado
│   │   ├── react-best-practices/         45+ regras React/Next.js
│   │   ├── postgres-best-practices/      32+ regras Postgres
│   │   ├── ui-ux-pro-max/               50 estilos, 21 paletas
│   │   └── ...                           + 22 outras skills
│   │
│   ├── workflows/                        28 processos (slash commands)
│   │   ├── master-orchestration.md       Orquestrador multi-agente
│   │   ├── error-recovery.md             Auto-recuperação de erros
│   │   ├── parallel-execution.md         Execução paralela
│   │   ├── grind.md                      Loop de correção automática
│   │   └── ...                           + 24 outros workflows
│   │
│   ├── GUIA_MESTRE_ORQUESTRACAO.md       Manual do orquestrador
│   └── scratchpad.md                     Memória de curto prazo
│
├── STATE.md                              Estado atual (boot ~300 tokens)
├── HANDOFF.md                            Briefing entre sessões
└── GUIAMESTRE.md                         Manual do sistema (~400 tokens)
```

<br/>

---

<br/>

## 🎯 Skills

29 skills carregadas **Just-In-Time** (JIT) — o agente só carrega quando precisa:

<br/>

### 🔧 Core — Operação do Agente

| Skill | O que faz |
|:---|:---|
| `executor` | Commits atômicos, desvio handling, checkpoints |
| `planner` | Planos em fases com dependências e verificação goal-backward |
| `plan-checker` | Valida planos antes de executar |
| `debugger` | Debug sistemático com estado persistente |
| `verifier` | Valida trabalho contra spec com evidência empírica |
| `empirical-validation` | Exige prova real antes de marcar como completo |
| `token-budget` | Rastreia budget de tokens para prevenir overflow |
| `self-improving-agent` | O agente melhora seus próprios processos |
| `intake` | Transforma ideias vagas em designs validados |

<br/>

### 💻 Desenvolvimento

| Skill | O que faz |
|:---|:---|
| `typescript-pro` | Generics, strict mode, decorators, patterns enterprise |
| `react-best-practices` | 45+ regras de performance React e Next.js |
| `zustand-store-ts` | Stores Zustand tipados com middleware |
| `zod-validation-expert` | Validação Zod + React Hook Form + tRPC |

<br/>

### 🗄️ Backend & Database

| Skill | O que faz |
|:---|:---|
| `postgres-best-practices` | 32+ regras: índices, queries, locks, monitoring |
| `edge-functions` | Edge Functions com Deno: CORS, cold start, errors |
| `migrations` | Migrations seguras com RLS, multi-tenant, rollback |
| `backend-security-coder` | Input validation, auth, API security (OWASP) |
| `auth-implementation-patterns` | Auth e autorização enterprise-grade |

<br/>

### 🎨 UI/UX & Design

| Skill | O que faz |
|:---|:---|
| `ui-ux-pro-max` | 50 estilos, 21 paletas, 50 font pairings, 9 stacks |

<br/>

### 🚀 Deploy & DevOps

| Skill | O que faz |
|:---|:---|
| `vercel-deployment` | Deploy Vercel + Next.js: domínios, edge config |
| `vercel-automation` | Automação via MCP: deployments, DNS, env vars |
| `vercel-ai-sdk-expert` | Vercel AI SDK: useChat, streaming, tools |
| `git-hooks-automation` | Husky, lint-staged, commitlint |
| `git-workflow` | Workflow Git estruturado para equipes |

<br/>

### 🤖 AI & LLM

| Skill | O que faz |
|:---|:---|
| `gemini-api-dev` | Gemini API: multimodal, streaming, function calling |
| `gemini-api-integration` | Integração Gemini em projetos de produção |
| `llm-ops` | RAG, embeddings, vector DB, fine-tuning, evals |
| `rag` | Retrieval Augmented Generation |

<br/>

---

<br/>

## 🔄 Workflows

28 processos passo-a-passo ativados via slash commands:

<br/>

### 🎯 Orquestração & Planejamento

| Comando | Para que serve |
|:---|:---|
| `/master-orchestration` | Orquestração multi-agente (>10 tasks) |
| `/decompose-task` | Decomposição em sub-tasks com dependências |
| `/parallel-execution` | Sub-agentes simultâneos (reduz tempo 60-70%) |
| `/plan` | Dry-run: pensar primeiro, codar depois |

<br/>

### 🔧 Desenvolvimento & Debug

| Comando | Para que serve |
|:---|:---|
| `/grind` | Loop de correção automática até passar |
| `/debug` | Análise de causa raiz e diagnóstico |
| `/code-review` | Review de segurança e qualidade |
| `/create-component` | Criar componente seguindo padrões |

<br/>

### 🧠 Contexto & Memória

| Comando | Para que serve |
|:---|:---|
| `/context-handoff` | Transferir contexto entre sessões |
| `/read-project-state` | Leitura real-time do estado do projeto |
| `/token-budget` | Economia de tokens (contexto grande) |
| `/session-recovery` | Restaurar sessão quebrada |

<br/>

### 🚀 Deploy & Segurança

| Comando | Para que serve |
|:---|:---|
| `/deploy-safe` | Deploy seguro com verificação e rollback |
| `/security` | Regras: .env, secrets, RLS, OWASP |
| `/feature-flag` | Toggle ON/OFF para features de risco |
| `/testing` | Padrões: Vitest, pgTAP, Playwright |
| `/migrations` | Migrations com RLS e rollback |

<br/>

### 🔬 Qualidade & Validação

| Comando | Para que serve |
|:---|:---|
| `/self-audit` | Checklist antes de marcar como ✅ |
| `/validate-integration` | Validação frontend ↔ backend ↔ DB |
| `/error-recovery` | Auto-recuperação: retry, escalação, rollback |
| `/self-improving` | O agente aprende com erros anteriores |

<br/>

---

<br/>

## ⚙️ Como Funciona

<br/>

### 1. A Lei dos 3.300 Tokens

O agente opera com um budget fixo para evitar degradação de qualidade:

```
AGENT_BOOTSTRAP     ~500 tokens    regras do sistema
STATE.md            ~300 tokens    estado do projeto
Spec da tarefa      ~200 tokens    o que fazer
Skill JIT           ~800 tokens    habilidade carregada
LSFS (busca)      ~1.500 tokens    código via grep/search
────────────────────────────────
TOTAL             ~3.300 tokens
```

> Se precisa de mais contexto → **quebra em fases** ao invés de sobrecarregar.

<br/>

### 2. LSFS — Lazy Smart File Search

O agente **nunca** lê arquivos inteiros:

```
1. grep_search        → encontra linhas relevantes
2. view_file_outline  → entende a estrutura
3. view_file (range)  → lê só o necessário
```

> Economia de **70-80% dos tokens** em projetos grandes.

<br/>

### 3. Memória entre Sessões

```
Sessão 1:  "Implementei login, passo 3 de 5. Falta logout e reset."
           → Salva em STATE.md + HANDOFF.md

Sessão 2:  Agente lê STATE.md no boot
           → "Login pronto. Vou começar pelo logout."
```

<br/>

### 4. Error Recovery em 3 Níveis

```
Nível 1:  Erro leve (typo, import)        → auto-correção silenciosa
Nível 2:  Erro estrutural (approach)       → tenta alternativa, notifica
Nível 3:  Falha crítica (tudo quebrou)     → git stash → documenta → pede ajuda
```

<br/>

### 5. Orquestração Multi-Agente

```
Humano:        "Implementa autenticação completa"
                         │
Orquestrador:  Decompõe em 8 sub-tasks
                         │
               Tasks 1,2,3 (independentes) → PARALELO
                         │
               Task 4 (depende de 1) → aguarda → executa
                         │
               Self-audit → 5 filtros por task
                         │
               Validate-integration → tudo junto
                         │
               HANDOFF atualizado → próxima sessão
```

<br/>

---

<br/>

## 🎛️ Configuração Avançada

### Adaptando ao Hardware

```markdown
# Em .agents/rules/core-rules.md

# 8GB RAM (modesto):
Máx 2 sub-agentes paralelos. Serializar se CPU > 80% ou RAM > 6GB.

# 16GB+ RAM (potente):
Máx 4 sub-agentes paralelos. Serializar se CPU > 90% ou RAM > 12GB.
```

<br/>

### Criando Skills Customizadas

```bash
mkdir -p .agents/skills/minha-skill/

cat > .agents/skills/minha-skill/SKILL.md << 'EOF'
---
name: Minha Skill Custom
description: Descreva o que ela faz
---
# Instruções para o Agente
[Suas regras e padrões aqui]
EOF
```

<br/>

### Criando Workflows Customizados

```bash
cat > .agents/workflows/meu-workflow.md << 'EOF'
---
description: Descrição do workflow
---
## Passos
1. Primeiro passo
2. Segundo passo
3. Terceiro passo
EOF
```

Ativar: digite `/meu-workflow` no Gemini CLI.

<br/>

---

<br/>

## 📈 Benchmarks

Métricas de 3+ meses de uso intensivo em produção:

| Métrica | Sem OS | Com OS | Melhoria |
|:---|:---:|:---:|:---:|
| Tokens por sessão | ~50k | ~12k | **-76%** |
| Tasks sem intervenção | ~30% | ~85% | **+183%** |
| Erros não recuperados | ~40% | ~5% | **-87%** |
| Context loss entre sessões | 100% | ~5% | **-95%** |
| Tempo (tarefas 10+ steps) | 3-4h | 45min-1h | **-75%** |

<br/>

---

<br/>

## ❓ FAQ

**Funciona com qualquer projeto?**
Sim. O OS é agnóstico de framework/linguagem. Skills cobrem React, Next.js, Vue, Svelte, Flutter, PostgreSQL, Deno e mais.

**Preciso saber programar?**
Sim. O OS potencializa o agente, mas você precisa entender o que ele faz para direcionar.

**Funciona com o Gemini CLI gratuito?**
Sim. Projetado para respeitar limites de tokens e rate limits.

**Posso modificar skills e workflows?**
Absolutamente. Fork, customize, adicione skills do seu domínio.

**Substitui o Cursor / Windsurf / Copilot?**
Não é concorrente — é complementar. Foca em transformar o Gemini CLI em agente autônomo com memória e auto-correção.

**Posso contribuir?**
Sim! Fork, melhore, abra PRs.

<br/>

---

<br/>

<p align="center">
  <strong>Criado por Helday Sousa</strong>
</p>

<p align="center">
  <a href="https://instagram.com/heldaysousa">
    <img src="https://img.shields.io/badge/Instagram-@heldaysousa-E4405F?style=for-the-badge&logo=instagram&logoColor=white" alt="Instagram" />
  </a>
</p>

<p align="center">
  O Antigravity OS nasceu de <strong>centenas de horas</strong> de dev real com AI agents.<br/>
  Cada otimização, workflow e skill veio de uma dor real em produção.
</p>

<p align="center">
  Se te ajudou, dá uma ⭐ e me segue no Instagram.
</p>

<br/>

---

<p align="center">
  <strong>📜 Licença MIT</strong> — Use, modifique, distribua. Só dá os créditos.
</p>

<br/>

<p align="center">
  <em>🪐 O futuro não é sobre a AI substituir o dev.<br/>É sobre o dev ter uma AI que sabe trabalhar direito.</em>
</p>

<p align="center">
  <strong>Antigravity OS v4.0</strong> — por <a href="https://instagram.com/heldaysousa">Helday Sousa</a>
</p>
