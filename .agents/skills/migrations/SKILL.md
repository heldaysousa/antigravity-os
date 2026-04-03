# SKILL: migrations
Arquivo: supabase/migrations/[timestamp]_[descricao].sql
Sempre incluir: CREATE TABLE + RLS enable + policies + índices.
Rollback: todo migration tem arquivo _down correspondente.
Testar local: supabase db reset — ANTES de produção. NUNCA ALTER TABLE manual.
