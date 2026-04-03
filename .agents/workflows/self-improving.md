# /self-improving — Loop de Aprendizado Continuo

## Proposito
Ao final de cada tarefa concluida, extrair learnings e persistir no knowledge.db.

## Trigger
Executar automaticamente apos qualquer workflow que produza output avaliavel.

## Processo
1. Reviewer avalia o output final (confidence, erros, desvios)
2. Extrair learnings:
   - O que funcionou: skill X foi eficaz para intencao Y
   - O que falhou: template Z gerou confidence < 0.7 para objecao W
   - Ajuste sugerido: usar template A em vez de Z para objecao W
3. Persistir em .memory/knowledge.db (tabela knowledge, category=learning)
4. Atualizar decisions se houver mudanca de estrategia
5. Registrar no Flight Recorder: tipo=learning

## Formato do learning
{
  "id": "learn_[timestamp]",
  "content": "[o que foi aprendido]",
  "category": "learning",
  "tags": ["skill_usada", "intencao", "resultado"],
  "session_source": "self-improving-loop"
}
