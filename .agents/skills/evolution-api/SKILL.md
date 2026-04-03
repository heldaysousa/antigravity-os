# SKILL: evolution-api (WhatsApp via VPS Hostinger)
Deploy: Docker na VPS Hostinger (docker/evolution-api/Dockerfile)
Comunicação: Edge Function evolution-send → Evolution API REST na VPS
Webhook: Evolution API → unified-message-handler (Supabase Edge Function)
Rate limit: máx 1 msg/segundo por conversa.
Deduplicação: verificar message_id ANTES de processar (evitar duplicatas).
Reconexão: script evolution-reconnect-after-30min.sh para recuperação automática.
Fallback: IA indisponível → enfileirar para agente humano (tabela queue_human).
Estado: persistir SEMPRE em tabela conversations, nunca só em memória.
Instâncias: múltiplas instâncias por tenant, isoladas por tenant_id.
