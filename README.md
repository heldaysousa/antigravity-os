# 🚀 Antigravity OS - INNFLOW Engine

**Status:** ✅ **ATIVO**
**Versão:** 4.0 (Gemini CLI)
**Data:** Abril 2026

---

## 🌌 VISÃO GERAL

Este repositório contém o **Antigravity OS** (Sistema Operacional de Agência AI) customizado e tunado especialmente para o projeto **INNFLOW Engine**. 

Ele centraliza todos os protocolos, regras de bootstrap, skills otimizadas, workflows e base de conhecimento configurada para guiar o Model Context Protocol (MCP) e as sessões do Gemini CLI.

A principal missão deste repositório é garantir consistência cross-session, documentação on-the-fly, e paralelização de agentes em ambiente restrito de tokens e hardware.

---

## 🏗️ ARQUITETURA DE OTIMIZAÇÕES

```text
antigravity-os/
├── .agents/
│   ├── rules/            # Regras estritas do agente (State, System)
│   ├── skills/           # Habilidades customizadas (JIT loaded)
│   └── workflows/        # Processos de execução passo-a-passo
├── DOCSFINAL/            # Output estático gerado do projeto Innflow
└── *.md                  # Trackers de estado, roadmap, relatórios E2E
```

### Componentes Chave

- **`.agents/rules/`**: Base da Lei dos 3.300 Tokens, limites de HW, SQLite tuning e fallback de erros.
- **`.agents/skills/`**: 
  - `executor`: Commit atômico, checkpoint protocols.
  - `planner`: Tree of thoughts e decomposition phase.
  - E skills adicionadas de inteligência para Leads, Extração de Dados e Debug Global.
- **`.agents/workflows/`**: Orquestrador Mestre, Error Recovery, Token Budgeting, Handoff e Paralelização.
- **`HANDOFF.md` / `STATE.md`**: Tracking contextual que permite ao agente retomar projetos sem perder tempo lendo milhares de linhas antigas.

---

## ⚙️ CONFIGURAÇÃO & DEPLOY

Para utilizar esta arquitetura em um novo workspace Gemini / MCP:

1. Clone esse repositório no seu ambiente de desenvolvimento.
2. Copie o respectivo `.agents/` para a raiz do seu novo projeto em `.agents`.
3. Certifique-se que o Agent Bootstrap referencie esses arquivos (por padrão usa-se `~/.antigravity/AGENT_BOOTSTRAP.md` em harmonia com `.agents/rules/`).

---

## 📜 REGRAS MAGNAS (ANTIGRAVITY OS v4.0)

1. **LEI DOS 3.300 TOKENS:** Nunca sobrecarregar contexto. Sub-agentes assíncronos.
2. **LSFS OBRIGATÓRIO:** Busca otimizada (grep/code-search), zero leitura de docs massivos.
3. **HARDWARE AWARE:** Threshold em MBP Intel x64 (8GB RAM), paralelismo controlado.
4. **ERROR RECOVERY:** Resolução silenciosa + Knowledge DB antes de escalar para o usuário.

---

## 🔐 SEGURANÇA E CODE STANDARDS

- Audit trail local para rollback seguro.
- Isolamento de execução de Edge Functions Deno.
- Strict Typescript Rules aplicadas injetadas via skill `typescript-pro`.

---
## 🤝 SUPORTE E MANUTENÇÃO

Este OS evolui junto com o projeto INNFLOW.
Caso seja necessário refazer o bootstrap de contexto:
Execute `/context-handoff` para acionar a retomada inteligente.
