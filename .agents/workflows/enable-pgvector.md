# /enable-pgvector — Ativar RAG Remoto no Supabase

## Pre-requisito
MCP supabase deve estar ATIVO antes de executar este workflow.
Solicitar ao usuario: "Ative o MCP supabase para continuar."

## Passos (executar via MCP supabase)
1. Verificar se extensao pgvector esta ativa:
   SELECT * FROM pg_extension WHERE extname = 'vector';

2. Se nao ativa — executar no Supabase SQL Editor:
   CREATE EXTENSION IF NOT EXISTS vector;

3. Criar tabela de embeddings:
   CREATE TABLE IF NOT EXISTS knowledge_embeddings (
     id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
     tenant_id TEXT NOT NULL,
     content TEXT NOT NULL,
     embedding vector(1536),
     category TEXT DEFAULT 'general',
     created_at TIMESTAMPTZ DEFAULT NOW()
   );
   CREATE INDEX ON knowledge_embeddings USING ivfflat (embedding vector_cosine_ops);
   ALTER TABLE knowledge_embeddings ENABLE ROW LEVEL SECURITY;

4. Validar: SELECT COUNT(*) FROM knowledge_embeddings;

## Status
PENDENTE — aguardando ativacao do MCP supabase pelo usuario.
