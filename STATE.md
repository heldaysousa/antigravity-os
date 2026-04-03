# STATE — ESTADO ATUAL
Atualizar a cada sessao. Lido SEMPRE no boot (~300 tokens).
Ultima atualizacao: 22 de Marco de 2026 — 02:13

## FASE ATUAL
INNFLOW ENGINE v2 — DEPLOYADO EM PRODUÇÃO. Feature flag USE_ENGINE_V2 = ATIVO.
PLANO CORREÇÃO v3 — FASES 0-4 COMPLETAS.
OTIMIZAÇÃO ANTIGRAVITY OS — EM ANDAMENTO (M0 concluído, Módulos 1-11 pendentes).

## CONCLUIDO (22/03) — M0-M6 ANTIGRAVITY OS OTIMIZAÇÃO
- [x] fd binary ARM→x86_64 fix (Homebrew symlink override, find_by_name operacional)
- [x] Diagnóstico M0 v2 completo (10 seções, 6 issues documentadas)
- [x] M1 MCP Cleanup: 2 ativos (filesystem/github), 3 já disabled. ⚠️ Tokens plaintext detectados
- [x] M2 Skills Path Fix: 36 skills mescladas .agent/→.agents/skills/ (37 total, 34 com SKILL.md)
- [x] M3 MCP+Bootstrap: mcpconfig duplicado mesclado→mcp_config.json (6 MCPs/2 ativos), AGENT_BOOTSTRAP v2 unificado
- [x] M4 Rules+Workflows: 2 rules criadas (core-rules, model-router), 2 workflows novos (session-recovery, new-feature), 23 total
- [x] M5 Arquivos Âncora: GUIAMESTRE.md (~400t) e HANDOFF.md template criados na raiz
- [x] M6 Migração Memory: knowledge.json (.bak) → knowledge.db SQLite (31 facts, 6 decisions)

## CONCLUIDO (20/03) — OTIMIZAÇÃO ANTIGRAVITY OS
- [x] Auditoria de configs (23 issues, 6 críticos) documentada
- [x] 68 skills do awesome-skills mapeadas em 3 prioridades
- [x] knowledge.json populado com 16 entries
- [x] .gitignore corrigido (.hapi.yaml, GITHUB-TOKEN, diagnose-*.json)
- [x] Pre-commit hook v2 (separado .env/secrets check do lint)
- [x] turbo.json limpo (Wasender removido, remoteCache off, lint deps fix)
- [x] VSCode settings.json configurado (formatter, lint, TS, Tailwind)
- [x] config.toml atualizado (Wasender removido, Evolution+Engine+Webhooks)
- [x] STATE.md atualizado + ROADMAP.md corrigido
- [x] Boot protocol LLM-agnostic criado
- [x] Session snapshots (15 min) implementado
- [x] 18 awesome-skills instaladas (global + projeto)

## CONCLUIDO (18/03) — FASE 4 + Sales + UX
- [x] 35 hooks limpos de @ts-nocheck, type-check OK, RPCs 12/12
- [x] Sales Engine completo (catálogo, aprovação, triggers, sons, metas)
- [x] UX Supremacy (glassmorphism, logos, dark OLED, tipografia)

## CONCLUIDO (17-18/03) — FASES 0-3
- [x] Wasender → Evolution API migração completa
- [x] Engine V2 deploy + feature flag + RAG 3-tier
- [x] Gemini models GA + Docker otimizado + RPCs 12/12

## ARQUITETURA ATUAL
innflow-engine (V2) convive com unified-message-handler (fallback)
Adoção gradual via feature flag: USE_ENGINE_V2=true (Supabase secrets)
evolution-webhook roteia automaticamente com fallback

## MAPEAMENTO SKILLS -> EDGE FUNCTIONS
- intake_skill     -> innflow-engine/grupo-A (built-in)
- extract_skill    -> extract-contact-data (existente)
- score_skill      -> lead-scoring (existente)
- respond_skill    -> chat-ai (existente + RAG fix)
- crm_skill        -> innflow-engine/grupo-D (built-in)
- broadcast_skill  -> innflow-engine/grupo-D (built-in)
- onboarding       -> onboarding-interview (ATIVO)
- retroalimentacao -> retroalimentacao (ATIVO)

## BUGS P0 CONHECIDOS
- LINT-001: 51 erros lint em apps/admin (workaround: pre-commit non-blocking)

## OPEN BLOCKERS
- Nenhum blocker crítico. LINT-001 é non-blocking.

## NEXT ACTION
1. Limpeza do working tree (507 arquivos sujos — Módulo 4)
2. Deploy-safe: Migration → Edge Functions → Frontend → Vercel
3. Ativar TypeScript strict progressivamente (noImplicitAny primeiro)
- [x] M7: Skills globais copiadas do BKP
- [x] M9: Arquivos flutuantes movidos para .archive/
- [x] M7: Skills globais copiadas do BKP (    1443)
