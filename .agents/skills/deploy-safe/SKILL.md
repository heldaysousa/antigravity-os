# SKILL: deploy-safe
Sequência OBRIGATÓRIA: 1. Migration → 2. Edge Functions → 3. Frontend
Deploy Evolution API: SSH na VPS Hostinger → docker pull → docker-compose up -d
Pre-deploy checklist:
  [ ] Testes E2E passando (Playwright)
  [ ] Sem .env hardcoded
  [ ] RLS verificado nas tabelas afetadas
  [ ] Migration testada local
  [ ] Branch atualizada com main
  [ ] Evolution API health check na VPS
Deploy: Vercel CLI (frontend) + Supabase CLI (migrations + functions) + SSH (Evolution API)
Rollback: git revert + supabase db reset (EMERGÊNCIA)
