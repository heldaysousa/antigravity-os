# GUIA MESTRE v2.4 — Antigravity OS
> Leia antes de qualquer ação. Adapte ao seu projeto.

## BOOT OBRIGATÓRIO (toda sessão — 3 passos, budget: ~1.100t)
1. LER `STATE.md` → fase atual, blocker, NEXT_ACTION (~300t)
2. CONFIRMAR orientação em ≤ 2 linhas antes de executar qualquer coisa
3. APLICAR Lei do Contexto abaixo

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
- `/context-handoff` → transferir contexto entre sessões
- `/error-recovery` → auto-recuperação de erros
- `/parallel-execution` → execução paralela de sub-agentes
- `/self-audit` → auto-auditoria antes de marcar como done
- `/token-budget` → economia de tokens
- `/plan` → dry-run antes de codar
- `/grind` → loop de correção automática
- `/debug` → análise de causa raiz
- `/code-review` → review de segurança e qualidade
- `/master-orchestration` → orquestração multi-agente
- `/feature-flag` → toggle ON/OFF para features de risco
- `/deploy-safe` → deploy seguro com rollback
- `/security` → regras de segurança
- `/testing` → padrões de testes
- `/validate-integration` → validação cross-layer
- `/decompose-task` → decomposição de tasks complexas

## REGRAS DE OURO
1. **LSFS:** NUNCA ler arquivos inteiros. Usar grep_search → view_file_outline → view_file com ranges
2. **Budget:** Tarefa > 3.300 tokens → QUEBRAR EM FASES
3. **Memória:** Atualizar STATE.md ao fim de cada sessão
4. **Segurança:** NUNCA commitar .env, secrets, tokens
5. **Validação:** Rodar type-check/build antes de marcar como done
