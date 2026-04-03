# /handoff
1. Ler STATE.md — capturar fase atual + NEXT_ACTION
2. Rodar git diff --name-only HEAD
3. Gerar HANDOFF.md com: modelo anterior | task | arquivos modificados | feito | falta | blocker
4. Salvar snapshot em .logs/session_recorder.db
5. Instruir: "Leia AGENT_BOOTSTRAP.md + STATE.md + HANDOFF.md e execute NEXT_ACTION"
