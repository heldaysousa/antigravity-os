---
description: Regras de segurança Antigravity OS — Nunca commitar .env, secrets, tokens. Regras de RLS, auth, OWASP.
---

# Segurança Antigravity OS

## Regra #1: NUNCA commitar secrets

### O que NÃO pode ser commitado

- `.env`, `.env.local`, `.env.production`, `.env.development`
- Chaves de API (GEMINI_API_KEY, SUPABASE_SERVICE_ROLE_KEY, etc.)
- Tokens (GitHub, Vercel, Supabase)
- Senhas, URLs com credenciais
- `secrets.json`, `credentials.*`, `*.pem`

### O que PODE ser commitado

- `.env.example` com placeholders vazios
- Código que LÊ variáveis de ambiente (`Deno.env.get()`, `import.meta.env.VITE_*`)

### Verificação

// turbo

```bash
git diff --cached --name-only | grep -E "\.env$|\.env\.|secret|credential|\.pem$"
```

Se retornar algo → **NÃO commitar!**

## Regra #2: Multi-tenant (RLS)

- **RLS** habilitado em TODAS as tabelas com dados de tenant
- **org_id** em TODAS as queries
- **Policies** de SELECT, INSERT, UPDATE, DELETE
- **Isolamento** entre organizações verificado

## Regra #3: Auth (JWT)

- Verificar `Authorization: Bearer` header
- Validar JWT antes de processar
- Verificar ownership do recurso
- Rate limiting em endpoints públicos

## Regra #4: Input Validation

- Validar UUID format
- Validar length de strings
- Sanitizar inputs contra XSS
- Usar Zod para schemas de validação

## Regra #5: OWASP API Top 10

1. ✅ Broken Object Level Authorization → RLS + org_id
2. ✅ Broken Authentication → JWT + session management
3. ✅ Excessive Data Exposure → SELECT específico
4. ✅ Lack of Resources & Rate Limiting → rate limiter
5. ✅ Broken Function Level Authorization → RBAC
6. ✅ Mass Assignment → whitelist de campos
7. ✅ Security Misconfiguration → CORS, headers
8. ✅ Injection → Parameterized queries
9. ✅ Improper Assets Management → documentação de APIs
10. ✅ Insufficient Logging → logs de auditoria

## Checklist Auditoria

- [ ] Nenhum secret no código commitado
- [ ] .gitignore inclui .env e variantes
- [ ] RLS habilitado em todas as tabelas
- [ ] JWT verificado em todos os endpoints
- [ ] Inputs validados e sanitizados
- [ ] Logs de auditoria para ações sensíveis
- [ ] Rate limiting em endpoints públicos
- [ ] CORS configurado (não wildcard em prod)
