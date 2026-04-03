# /session-restore

Proposito: reconstruir contexto completo a partir do Flight Recorder DB.

1. Conectar em .logs/session_recorder.db
2. Buscar ultimo snapshot: SELECT * FROM snapshots ORDER BY id DESC LIMIT 1
3. Buscar eventos recentes: SELECT * FROM events WHERE snapshot_id = [ultimo_id] ORDER BY created_at DESC LIMIT 10
4. Buscar ultima troca de modelo: SELECT * FROM llm_switches ORDER BY id DESC LIMIT 1
5. Reconstruir estado em 5 linhas:
   - Modelo anterior: [model]
   - MCPs ativos: [mcp_active]
   - Branch: [branch]
   - Tarefa: [task]
   - Resumo: [state_summary]
6. Carregar STATE.md e confirmar alinhamento com snapshot
7. Apresentar: "Sessao restaurada. Fase: X | Blocker: Y | NEXT_ACTION: Z"
