---
name: Contact Data Extractor
description: Extrai entidades críticas (Nome, Tel, Email, Intenção) da mensagem solta. Fase B do INNFLOW Engine.
---

# Extract Skill (Data Parsing)

Skill chamada de forma paralela ao RAG e Histórico (Grupo B) para processar o `raw_text` do input (WhatsApp ou Insta) e retornar JSON consolidado.

## Features Críticas de Extração Base:
- `nome` (Opcional - só preenche se for inequívoco)
- `email` (Opcional - detecção RegExp simples)
- `tel` (Geralmente validado pelo provider - fallback)
- `cidade` / `estado` (Se referenciado)
- `interesse` / `produto` (Qual curso, produto, ou dor)
- `intent` (O que ele quer? agendamento, compra, duvida_produto, suporte)
- `emocao` (happy, neutral, sad, angry, stressed)

## Limites Otimizados
A execução paralela visa < 200ms de latência pra extração de dados usando modelos rápidos (ex: Flash ou Haiku) com function calling `tools: [{ "name": "extract_entities" }]`.
