# SKILL: supabase-postgres-best-practices
RLS obrigatório em TODA tabela. NUNCA desativar em produção.
Migrations: supabase/migrations/[timestamp]_[desc].sql — NUNCA ALTER TABLE manual.
Edge Functions: Deno/TypeScript, imports por URL ou npm: prefix.
pgvector: índice IVFFlat para embeddings da knowledge base de IA.
Multi-tenant: tenant_id em TODAS as tabelas com índice obrigatório.
Auth: auth.uid() em policies, nunca UUIDs hardcoded.
Queries: tipo de retorno explícito, nunca SELECT *.
RLS template:
  ALTER TABLE [t] ENABLE ROW LEVEL SECURITY;
  CREATE POLICY "tenant_isolation" ON [t] USING (tenant_id = auth.uid());
