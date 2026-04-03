---
description: Padrões de queries Supabase para Antigravity OS com RLS multi-tenant, performance e error handling. Usar para qualquer operação CRUD no Supabase.
---

# Supabase Queries (Padrões Antigravity OS)

## Regra #1: Multi-Tenant (org_id SEMPRE presente)

```ts
// CORRETO — org_id em TODAS as queries multi-tenant
const { data, error } = await supabase
  .from("contacts")
  .select("id, name, status, created_at")
  .eq("org_id", orgId)
  .order("created_at", { ascending: false })
  .limit(50);

// ERRADO — sem org_id = vazamento de dados
const { data } = await supabase.from("contacts").select("*");
```

## Padrões por Operação

| Operação | Padrão                                              |
| -------- | --------------------------------------------------- |
| SELECT   | `.select('cols').eq('org_id', id).order().limit()`  |
| INSERT   | `.insert({ org_id, ... }).select().single()`        |
| UPDATE   | `.update({ ... }).eq('id', id).eq('org_id', orgId)` |
| DELETE   | `.delete().eq('id', id).eq('org_id', orgId)`        |

## Performance

### Select específico (nunca SELECT \*)

```ts
.select('id, name, status, created_at')  // Apenas colunas necessárias
```

### Paginação

```ts
.range(0, 49)  // Primeiros 50
// ou
.limit(50).offset(page * 50)
```

### Eager loading (evitar N+1)

```ts
.select(`
  id, name,
  pipeline_stage:stages(name, color)
`)
```

## Error Handling

```ts
const { data, error } = await supabase.from("table").select("*");

if (error) {
  console.error("Supabase error:", error.message);
  throw new Error(`Falha ao buscar dados: ${error.message}`);
}

if (!data || data.length === 0) {
  return [];
}

return data;
```

## Índices Obrigatórios

Garantir indexes em:

- `org_id` (todas as tabelas multi-tenant)
- `user_id` (tabelas por usuário)
- `created_at` (tabelas com ordenação temporal)

## Checklist

- [ ] org_id/user_id em TODOS os filtros multi-tenant
- [ ] Select específico (não SELECT \*)
- [ ] Limit/range para paginação (<50 por fetch)
- [ ] Error handling presente
- [ ] Indexes em colunas filtradas
