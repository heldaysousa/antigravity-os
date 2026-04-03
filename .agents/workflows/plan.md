---
description: Ativa o "Plan Mode". Força o agente a pensar, listar os arquivos que vai modificar e solicitar aprovação explícita sua antes de cometer qualquer código (Dry-Run).
---

# 📝 Plan Mode (Planejamento Estratégico)

## INSTRUÇÃO PARA O AGENTE (LLM):

Quando o usuário usar `/plan`, você fica **estritamente proibido de editar ou criar arquivos imediatamente**. Você deve entrar em "Dry-Run" (Simulação).

### Ações Exigidas:

1. **Compreensão:** Leia os arquivos relevantes usando `view_file` ou `grep_search` e entenda o contexto profundamente.
2. **Decomposição:** Gere uma lista de passos (Step-by-step).
3. **Diff Simulado:** Mostre exatamente quais arquivos serão afetados e _qual_ lógica será introduzida neles (sem escrever o código inteiro, focar em arquitetura).
4. **Aguardar:** Adicione no final da sua resposta uma pergunta grande em negrito:

**"O PLANO ESTÁ APROVADO? RESPONDA 'SIM' PARA EU COMEÇAR OU PEÇA ALTERAÇÕES."**

Não execute chamadas à ferramenta `write_to_file` ou `multi_replace_file_content` sob **nenhuma circunstância** até que o usuário tenha lido sua estratégia e dado aprovação no próximo turno da conversa.
