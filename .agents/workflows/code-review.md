---
description: Code review de segurança e qualidade Antigravity OS. Verificar RLS, SQL injection, TypeScript strict, performance, secrets.
---

# Code Review (Padrões Antigravity OS)

## Checklist de Review

### 1. Segurança (CRÍTICO)

- [ ] **RLS policies** corretas? org_id em todas as tabelas multi-tenant?
- [ ] **SQL injection** prevenida? Parâmetros escapados?
- [ ] **Secrets** expostos? API keys, tokens no código?
- [ ] **Inputs validados**? Zod ou checagem manual?
- [ ] **Auth** verificada em todos os endpoints?
- [ ] **CORS** configurado corretamente?

### 2. TypeScript

- [ ] **Strict mode** ativo? Zero `any`?
- [ ] **Props tipadas** com interfaces?
- [ ] **Return types** explícitos em funções públicas?
- [ ] **Null checks** adequados?

### 3. Performance

- [ ] **Indexes** em colunas filtradas (org_id, user_id, created_at)?
- [ ] **N+1 queries** evitadas? Eager loading com joins?
- [ ] **SELECT específico** (não SELECT \*)?
- [ ] **Paginação** com limit/range?
- [ ] **useShallow** em selects do Zustand?

### 4. Qualidade

- [ ] **Código simples** e legível?
- [ ] **Nomes** de funções/variáveis claros?
- [ ] **Sem duplicação** de código?
- [ ] **Error handling** adequado?
- [ ] **Testes** cobrindo mudanças?

### 5. Edge Functions (Deno)

- [ ] `Deno.env.get()` (nunca process.env)?
- [ ] CORS headers em todas as respostas?
- [ ] Error codes HTTP corretos (400/401/403/500)?
- [ ] Sem logs de dados sensíveis?

### 6. Multi-tenant

- [ ] **org_id** em TODOS os queries de dados de tenant?
- [ ] **RLS** habilitado em tabelas com dados de tenant?
- [ ] **Isolamento** entre tenants verificado?
- [ ] **Dados de outros tenants** nunca expostos?

## Output do Review

```markdown
## 🔴 Crítico (must fix)

1. [Issue]: [Descrição + como corrigir]

## 🟡 Warning (should fix)

1. [Issue]: [Descrição + sugestão]

## 🟢 Sugestão (nice-to-have)

1. [Melhoria]: [Descrição]

## ✅ Pontos Positivos

- [O que está bem feito]
```

## Regras do Reviewer

- Sempre verificar `git diff` antes de revisar
- Focar em arquivos modificados
- Priorizar: Segurança > Correctness > Performance > Style
- Incluir exemplos de como corrigir issues
- Não aceitar claims sem verificar
