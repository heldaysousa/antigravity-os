---
description: Padrões para Edge Functions Supabase (Deno/TypeScript). SEMPRE Deno.env.get, CORS, error handling, cold start optimization.
---

# Edge Functions Supabase (Padrões INNFLOW)

## Regras Deno Obrigatórias

- ✅ `Deno.env.get('VAR')` — **NUNCA** `process.env`
- ✅ Importar CORS de `../_shared/cors.ts`
- ✅ Importar Supabase client de `../_shared/supabase.ts`
- ✅ Retornar erros como 400/401/403/500 com `{error, code}`
- ✅ `verify_jwt: true` por padrão; `false` apenas para webhooks
- ✅ Validar body antes de processar
- ❌ **NUNCA** logar password, tokens ou PII

## Template Base

```ts
import { serve } from "https://deno.land/std@0.208.0/http/server.ts";
import { corsHeaders } from "../_shared/cors.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

serve(async (req) => {
  // Handle CORS preflight
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: corsHeaders });
  }

  try {
    // Auth
    const authHeader = req.headers.get("Authorization");
    if (!authHeader) {
      return new Response(
        JSON.stringify({
          error: "Missing authorization",
          code: "AUTH_MISSING",
        }),
        {
          status: 401,
          headers: { ...corsHeaders, "Content-Type": "application/json" },
        },
      );
    }

    // Supabase client
    const supabase = createClient(
      Deno.env.get("SUPABASE_URL")!,
      Deno.env.get("SUPABASE_ANON_KEY")!,
      { global: { headers: { Authorization: authHeader } } },
    );

    // Verify user
    const {
      data: { user },
      error: authError,
    } = await supabase.auth.getUser();
    if (authError || !user) {
      return new Response(
        JSON.stringify({ error: "Unauthorized", code: "AUTH_INVALID" }),
        {
          status: 401,
          headers: { ...corsHeaders, "Content-Type": "application/json" },
        },
      );
    }

    // Parse body
    const body = await req.json();

    // Business logic here...
    const result = { success: true };

    return new Response(JSON.stringify(result), {
      status: 200,
      headers: { ...corsHeaders, "Content-Type": "application/json" },
    });
  } catch (error) {
    console.error("Edge Function error:", error.message);
    return new Response(
      JSON.stringify({ error: "Internal error", code: "INTERNAL_ERROR" }),
      {
        status: 500,
        headers: { ...corsHeaders, "Content-Type": "application/json" },
      },
    );
  }
});
```

## CORS Shared

```ts
// supabase/functions/_shared/cors.ts
export const corsHeaders = {
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Headers":
    "authorization, x-client-info, apikey, content-type",
  "Access-Control-Allow-Methods": "POST, GET, OPTIONS",
};
```

## Cold Start Optimization

```ts
// Usar Deno.openKv() para cache local
const kv = await Deno.openKv();

// Cache de dados que não mudam frequentemente
const cached = await kv.get(["config", orgId]);
if (cached.value) {
  return cached.value;
}
```

## Deploy

```bash
# Test local
supabase functions serve [nome]

# Deploy
supabase functions deploy [nome]

# Logs
supabase functions logs [nome]
```

## Checklist

- [ ] Deno.env.get (nunca process.env)
- [ ] CORS headers em todas as respostas
- [ ] Auth verificada
- [ ] Error handling com códigos HTTP corretos
- [ ] Body validado antes de processar
- [ ] Sem logs de dados sensíveis
- [ ] deno.json com imports corretos
