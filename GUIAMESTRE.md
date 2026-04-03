# GUIA MESTRE v2.4 — InnFlow × Antigravity OS
> Leia antes de qualquer ação. Última atualização: Mar 2026

## BOOT OBRIGATÓRIO (toda sessão — 3 passos, budget: ~1.100t)
1. LER `STATE.md` → fase atual, blocker, NEXT_ACTION (~300t)
2. LER `PROJECT.md` → stack, regras de negócio (~800t)
3. CONFIRMAR orientação em ≤ 2 linhas antes de executar qualquer coisa

## LEI DO CONTEXTO (hard limit: 3.300 tokens/chamada)
| Camada | Tokens Estimados |
|--------|------------------|
| AGENT_BOOTSTRAP | ~500t |
| STATE.md | ~300t |
| Spec da tarefa | ~200t |
| Skill JIT (máx 1) | ~800t |
| Blocos LSFS | ~1.500t |
| **TOTAL MAX** | **3.300t** |

## SLASH COMMANDS (.agents/workflows/)
- `/session-recovery` → restaurar sessão quebrada
- `/new-feature` → criar feature com spec 5 campos
- `/deploy-safe` → protocolo de deploy
- `/context-handoff` → transição fim de dia/modelo
- `/code-review`, `/debug`, `/error-recovery`, `/feature-flag`, `/grind`, `/master-orchestration`, `/migrations`, `/parallel-execution`, `/plan`, `/read-project-state`, `/security`, `/self-audit`, `/supabase-queries`, `/testing`, `/token-budget`, `/validate-integration`

## MODEL ROUTER (.agents/rules/model-router.md)
Flash 3.0 → rotina | Pro 3.1 low/high → média a crítica | Sonnet 4.6 → debug complexo | Opus → irreversível (<5%)

## PROIBIÇÕES ABSOLUTAS (.agents/rules/core-rules.md)
❌ ler arquivo inteiro sem LSFS (`codebase_search`, `view_file` range)
❌ gerar código sem spec de 5 campos
❌ commit com múltiplas mudanças lógicas
❌ histórico de chat no contexto do agente
❌ skill sem auditoria de segurança
