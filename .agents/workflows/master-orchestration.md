---
description: Protocolo de orquestração Antigravity OS v2.2 — aplicar em implementações complexas multi-agente (>10 tasks). Inclui paralelização, uso de MCP/CLI, economia de tokens, error recovery e self-audit.
---

// turbo-all

# Antigravity OS MASTER ORCHESTRATION — Antigravity Edition

## QUANDO APLICAR

**APLICAR quando:**

1. Usuário diz "Siga o protocolo de orquestração" ou "Use a regra master"
2. Plano com múltiplas fases/tasks (>10 tasks)
3. Coordenação de agentes especializados
4. Implementação completa de feature complexa

**NÃO APLICAR quando:** Tarefas simples (1-3 arquivos), debugging pontual, perguntas conceituais.

---

## REGRA DE OURO (INVIOLÁVEL)

**O Orquestrador NUNCA executa código diretamente.** Ele é um gerente de projeto.

- ✅ **PERMITIDO:** Delegar tarefas, acompanhar progresso, validar, coordenar sequência/paralelização, atualizar scratchpad
- ❌ **PROIBIDO:** Escrever código diretamente, pular validações, continuar após falhas sem retry

**Violação = ABORT e reiniciar com delegação correta.**

---

## PROTOCOLO DE 5 FASES

### FASE 0: PRÉ-REQUISITOS E SEGURANÇA

1. Verificar ambiente:
   // turbo

```bash
git status && git branch && node -v && pnpm -v
```

2. Criar checkpoint:

```bash
git checkout -b backup/before-[feature]-$(date +%Y%m%d-%H%M%S)
```

3. Criar/atualizar scratchpad no workspace

### FASE 1: DECOMPOSIÇÃO ANALÍTICA

Usar workflow: `/decompose-task`

- Input: Plano bruto do usuário
- Output: JSON com tasks, agentes, dependências, parallel_blocks, critical_path
- Incluir visualização ASCII das fases
- Verificar budget de tokens: `/token-budget`
- Identificar blocos paralelizáveis: `/parallel-execution`

### FASE 2: ENGENHARIA DE PROMPTS

Para cada task da Fase 1, criar instrução focada com:

- Metadata (Agente, Files, Dependencies, Duration, Priority)
- Validation Criteria
- Requirements específicos
- Anti-patterns
- Success criteria

### FASE 3: VALIDAÇÃO ESTRATÉGICA

Usar workflow: `/validate-integration`

- Consistência de dependências (DAG válido)
- Cobertura de agentes
- Integrações cross-layer
- Performance/tokens

### FASE 4: EXECUÇÃO ORQUESTRADA

Para cada task em execution_order:

1. Checar dependências
2. Atualizar scratchpad
3. **Tasks paralelas:** disparar tasks independentes em paralelo → `/parallel-execution`
4. Executar com instruções exatas da Fase 2
5. Auto-validar com `/self-audit` (TypeScript + testes + visual)
6. Se PASS → DONE, se FAIL → `/error-recovery` (até 3x) ou escalar via Nível 3
7. Mostrar progresso como tabela de status

### FASE 5: VALIDAÇÃO FINAL E DEPLOY

Usar workflow: `/deploy-safe`

1. TypeScript check: `pnpm run type-check`
2. Testes: `pnpm test`
3. Build: `pnpm run build`
4. Deploy staging: `vercel --preview`
5. Smoke tests
6. Aguardar aprovação do usuário
7. Deploy production
8. Monitorar 10min

---

## PARALELIZAÇÃO OBRIGATÓRIA

- Tasks em parallel_blocks DEVEM ser executadas em paralelo
- Usar branches separadas quando necessário:

```bash
git checkout -b task/[ID] main
```

- Merge após conclusão e validação
- Sincronizar APENAS quando houver dependência

---

## OTIMIZAÇÃO DE CONTEXTO

Usar workflow completo: `/token-budget`

- Cada task recebe APENAS o contexto necessário
- Documentos longos → resumo de max 1 página
- Não replicar contexto completo a cada passo
- Usar referências incrementais (status, próximo passo, bloqueios)
- Ao iniciar nova sessão → `/context-handoff`
- Ao encerrar sessão → atualizar scratchpad conforme template do `/context-handoff`

---

## ANTI-PATTERNS

- ❌ Orquestrador executando código → delegar
- ❌ Pular validações → sempre usar `/self-audit` após cada task
- ❌ Ignorar parallel blocks → usar `/parallel-execution`
- ❌ Pedir passo manual quando existe ferramenta → usar MCP/CLI primeiro
- ❌ Enviar contexto excessivo → usar `/token-budget`
- ❌ Iniciar sessão sem ler scratchpad → usar `/context-handoff`
- ❌ Declarar task concluída sem type-check → proibido
- ❌ Deixar código quebrado ao encerrar sessão → sempre git stash ou `/error-recovery`

---

## CHECKLIST PRÉ-EXECUÇÃO

- [ ] Git branch clean, backup criado
- [ ] Scratchpad lido e atualizado (`/context-handoff`)
- [ ] Plano com parallel_blocks identificados (`/parallel-execution`)
- [ ] Token budget verificado (`/token-budget`)
- [ ] Uso de ferramentas mapeado para cada passo
- [ ] Validações configuradas (`/self-audit`)
- [ ] Error recovery path definido (`/error-recovery`)

---

## WORKFLOWS DE SUPORTE (REFERÊNCIA RÁPIDA)

| Workflow                | Quando acionar                        |
| ----------------------- | ------------------------------------- |
| `/decompose-task`       | Fase 1 — quebrar em sub-tasks         |
| `/parallel-execution`   | Fase 4 — disparar tasks independentes |
| `/token-budget`         | Contexto ficou grande, ou >10 tasks   |
| `/context-handoff`      | Início/fim de sessão                  |
| `/self-audit`           | Antes de marcar task como ✅          |
| `/error-recovery`       | Task falhou                           |
| `/grind`                | Erros de TS/lint para auto-correção   |
| `/validate-integration` | Após múltiplas tasks cross-layer      |
| `/deploy-safe`          | Fase 5 — deploy final                 |
| `/debug`                | Erro não óbvio em Edge Function/RLS   |
| `/code-review`          | Antes de merge para main              |
