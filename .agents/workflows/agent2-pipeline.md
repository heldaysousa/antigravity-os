# /agent2-pipeline — Pipeline Completo InnFlow

## Fluxo
Task → Planner/Splitter → Skill Matcher → Specialists (JIT) → Reviewer → Flight Recorder

## Execucao para lead novo

### Passo 1 — Planner
- Receber: { message, channel, contact_id, tenant_id }
- Decompor em: intake → rag → respond
- Estimar complexidade e tokens por etapa

### Passo 2 — Skill Matcher
- intake: usar .agent/skills/intake/SKILL.md
- rag: usar .agent/skills/rag/SKILL.md
- respond: usar .agent/skills/respond/SKILL.md

### Passo 3 — Specialists (execucao sequencial)
1. @intake → classifica e pontua o lead
2. @rag → busca contexto (requer MCP supabase ativo)
3. @respond → gera mensagem final

### Passo 4 — Reviewer
- Validar: confidence > 0.7 | tenant_id presente | dentro do limite de chars
- Se falhou: escalar para humano
- Se OK: aprovar para envio

### Passo 5 — Flight Recorder
- Registrar snapshot em .logs/session_recorder.db
- Persistir learning em .memory/knowledge.db

## Teste de integracao
Para testar o pipeline sem MCP supabase:
- Simular rag_output com dados mockados
- Validar que intake → respond funciona de ponta a ponta
