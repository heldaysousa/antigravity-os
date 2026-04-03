# SKILL: edge-functions (Deno/Supabase)
Runtime: Deno — imports por URL ou npm: prefix (não Node.js).
Auth: verificar JWT via supabaseClient em TODA function pública.
CORS: headers em toda function pública.
Errors: try/catch → {error, message, code} estruturado.
Gemini: streaming para respostas longas, model tiering por budget.
Logs: {tenant_id, action, duration_ms} em toda function.
Timeout: máx 10s síncronas; background tasks para longas.
Functions críticas: chat-ai | evolution-send | triggers-engine | asaas-webhook | unified-message-handler
