---
description: Debug e troubleshooting Antigravity OS. Análise de causa raiz, instrumentação, diagnóstico de Edge Functions, RLS, queries.
---

# Debug e Troubleshooting

## Quando Usar

- Task falhou 2x consecutivas
- Erro não óbvio em Edge Functions, RLS, queries
- Performance degradada
- Deploy falhou

## Procedimento

### 1. Coletar Evidências

```bash
# Logs do sistema
git log --oneline -5

# Status do git
git status

# Erros de TypeScript
pnpm type-check 2>&1 | head -50

# Logs de Edge Functions
supabase functions logs [nome] --limit 20

# Logs de deploy
vercel logs [deployment-url]
```

### 2. Análise de Causa Raiz

```
Sintoma → Hipótese → Teste → Confirmação → Fix
```

1. **Sintoma:** O que está acontecendo
2. **Hipótese:** O que pode estar causando
3. **Teste:** Como validar a hipótese
4. **Confirmação:** A hipótese está correta?
5. **Fix:** Como corrigir

### 3. Diagnósticos Comuns

| Sintoma                 | Causa Provável               | Diagnóstico                                       |
| ----------------------- | ---------------------------- | ------------------------------------------------- |
| "permission denied"     | RLS policy incorreta         | `SELECT * FROM pg_policies WHERE tablename = 'x'` |
| "function not found"    | Edge Function não deployed   | `supabase functions list`                         |
| "CORS error"            | Headers faltando             | Verificar `corsHeaders` em Edge Function          |
| "process.env undefined" | Usando Node ao invés de Deno | Trocar por `Deno.env.get()`                       |
| "org_id null"           | Usuário sem organização      | Verificar `user_profiles`                         |
| "infinite loop"         | useEffect sem deps           | Verificar array de dependências                   |
| "stale data"            | React Query cache            | Invalidar query com `queryClient.invalidate()`    |
| "build failed"          | Import incorreto             | Verificar paths e extensões                       |

### 4. Edge Function Debug

```bash
# Serve local para debug
supabase functions serve [nome] --debug

# Invocar com mock
curl -X POST http://localhost:54321/functions/v1/[nome] \
  -H "Authorization: Bearer [token]" \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'
```

### 5. RLS Debug

```sql
-- Verificar policies ativas
SELECT schemaname, tablename, policyname, permissive, cmd, qual
FROM pg_policies
WHERE tablename = 'nome_tabela';

-- Testar como usuário específico
SET request.jwt.claims = '{"sub": "user-uuid", "role": "authenticated"}';
SELECT * FROM nome_tabela;
```

### 6. Performance Debug

```bash
# Identificar queries lentas
# Supabase Dashboard → Database → Query Performance

# Verificar indexes
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'nome_tabela';

# Explain analyze
EXPLAIN ANALYZE SELECT * FROM contacts WHERE org_id = 'x';
```

## Output do Debug

```markdown
## 🐛 Debug Report

### Sintoma

[Descrição clara do que está acontecendo]

### Causa Raiz

[O que está causando o problema]

### Evidência

[Logs, screenshots, outputs que comprovam]

### Fix Aplicado

[O que foi feito para corrigir]

### Verificação

[Como confirmar que o fix funcionou]

### Prevenção

[Como evitar que o problema volte]
```
