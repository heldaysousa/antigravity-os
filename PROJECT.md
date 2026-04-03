# INNFLOW — PROJECT MANIFEST
Ancora imutavel. Nao modificar sem aprovacao. Lida no boot de toda sessao.

## IDENTIDADE
- Produto: InnFlow — CRM com IA para atendimento via WhatsApp
- Status: Em Producao (MVP)
- Stack: React+Vite+TypeScript / Supabase / Vercel / Evolution API (VPS) / Gemini AI / Asaas

## ARQUITETURA
- Frontend: React+Vite / Tailwind / shadcn/ui / Design System Liquid Glass (Apple Tahoe 2026)
- Backend: Supabase (PostgreSQL + RLS + Edge Functions Deno)
- WhatsApp: Evolution API no Docker/VPS Hostinger (NAO e Wasender/Baileys)
- IA: Gemini 2.5 Flash (rotina) / Gemini 3.1 Pro (logica) / Claude Sonnet (debug/arq)
- Pagamentos: Asaas (sandbox ativo, producao futuro)
- Infra: Vercel (frontend) / Hostinger VPS (Evolution API) / Supabase Cloud

## MODULOS CORE
- auth/          -> PremiumLogin, Register, Checkout, MFA
- dashboard/     -> Conversations, KanbanV2, AITraining, LeadDetails, ForecastDashboard
- admin/         -> Gestao de clientes, limites de planos, logs, faturamento
- landing/       -> V2/V3/V4 (paginas de conversao ultra-premium)
- onboarding/    -> Step-by-step para novos tenants

## EDGE FUNCTIONS CRITICAS
chat-ai | evolution-send | triggers-engine | asaas-webhook | unified-message-handler

## REGRAS IMUTAVEIS (NUNCA VIOLAR)
1. RLS obrigatorio em TODA tabela Supabase
2. Commit: [tipo](escopo): descricao < 72 chars
3. Design System: APENAS tokens Liquid Glass — sem CSS improvisado
4. WhatsApp: APENAS via Evolution API na VPS (nunca direto)
5. Deploy: Migration -> Edge Functions -> Frontend (nunca inverter)
6. Multi-tenant: tenant_id em TODAS as tabelas + indice
7. .env nunca commitado
