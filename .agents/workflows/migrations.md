---
description: Migrations Supabase com padrões Antigravity OS — RLS obrigatório, multi-tenant, org_id, índices, rollback.
---

# Migrations Supabase (Padrões Antigravity OS)

## Regras Obrigatórias

1. **org_id** em TODAS as tabelas de dados (multi-tenant)
2. **RLS** habilitado em TODAS as tabelas com dados de tenant
3. **Índices** em org_id, user_id, created_at
4. **Timestamps** created_at e updated_at com defaults
5. **UUIDs** como primary keys (gen_random_uuid())

## Template de Migration

```sql
-- Migration: [YYYYMMDD]_[nome_descritivo].sql
-- Descrição: [O que esta migration faz]

-- 1. Criar tabela
CREATE TABLE IF NOT EXISTS public.nome_tabela (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  org_id UUID NOT NULL REFERENCES public.organizations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES auth.users(id),

  -- Campos específicos
  name TEXT NOT NULL,
  status TEXT DEFAULT 'active',

  -- Timestamps
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 2. Índices
CREATE INDEX IF NOT EXISTS idx_nome_tabela_org_id ON public.nome_tabela(org_id);
CREATE INDEX IF NOT EXISTS idx_nome_tabela_user_id ON public.nome_tabela(user_id);
CREATE INDEX IF NOT EXISTS idx_nome_tabela_created_at ON public.nome_tabela(created_at);

-- 3. RLS
ALTER TABLE public.nome_tabela ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see data from their organization
CREATE POLICY "Users can view own org data" ON public.nome_tabela
  FOR SELECT
  USING (org_id IN (
    SELECT org_id FROM public.user_profiles WHERE user_id = auth.uid()
  ));

-- Policy: Users can insert data for their organization
CREATE POLICY "Users can insert own org data" ON public.nome_tabela
  FOR INSERT
  WITH CHECK (org_id IN (
    SELECT org_id FROM public.user_profiles WHERE user_id = auth.uid()
  ));

-- Policy: Users can update own org data
CREATE POLICY "Users can update own org data" ON public.nome_tabela
  FOR UPDATE
  USING (org_id IN (
    SELECT org_id FROM public.user_profiles WHERE user_id = auth.uid()
  ));

-- Policy: Users can delete own org data
CREATE POLICY "Users can delete own org data" ON public.nome_tabela
  FOR DELETE
  USING (org_id IN (
    SELECT org_id FROM public.user_profiles WHERE user_id = auth.uid()
  ));

-- 4. Trigger updated_at
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trigger_updated_at
  BEFORE UPDATE ON public.nome_tabela
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at();
```

## Validação

// turbo

```bash
supabase db lint
```

// turbo

```bash
supabase db diff
```

## Deploy

```bash
supabase db push
```

## Rollback

```bash
# Ver migrations aplicadas
supabase migration list

# Reverter (CUIDADO em produção)
supabase db reset  # Apenas staging
```

## Checklist

- [ ] org_id presente e NOT NULL
- [ ] RLS habilitado e policies criadas
- [ ] Índices em colunas filtradas
- [ ] created_at e updated_at com defaults
- [ ] ON DELETE CASCADE onde apropriado
- [ ] supabase db lint sem erros
- [ ] supabase db diff vazio após push
