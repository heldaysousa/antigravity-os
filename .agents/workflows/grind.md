---
description: Ativa o "Grind Mode" do Antigravity. O agente entrará em loop de correção automática rodando type-checks e testes até o código passar sem erros, sem precisar da sua intervenção.
---

# 🔄 Grind Mode (Auto-Correção)

## INSTRUÇÃO PARA O AGENTE (LLM):

Ao receber o comando `/grind`, você **deve** entrar em um loop autônomo de verificação e correção.

### Passo a Passo do Grind Mode:

1. **Identificar o Alvo:** Pergunte ou deduza do contexto atual qual componente, arquivo ou funcionalidade acabou de ser implementada.
2. **Executar Validação:** Execute no terminal (com `WaitMsBeforeAsync` longo o suficiente para pegar a saída):
   - `pnpm run type-check` (ou `tsc --noEmit`)
   - Se for React: `pnpm run lint` ou equivalente.
   - Teste específico se existir: `pnpm test [nome-do-arquivo]`
3. **Analisar Erros (O Grind):**
   - Se os comandos falharem (código de saída não zero ou erros explícitos no stdout), você NÃO deve parar e esperar pelo usuário.
   - Use as ferramentas `multi_replace_file_content` ou `replace_file_content` para corrigir os erros de TypeScript/Lint indicados no terminal.
4. **Repetir:**
   - Execute o passo 2 novamente.
   - Se falhar, repita a correção (Passo 3).
   - Limite o Grind Loop a um **máximo de 3 tentativas** para evitar consumir tokens infinitamente.
5. **Reportar:**
   - Ao finalizar (sucesso ou limite de 3 tentativas), informe ao usuário um relatório ASCII do que foi corrigido.

## EXCEÇÃO:

Se o servidor/código retornar erro, você NÃO pode dizer: _"Usuário, aqui está o erro, por favor corrija"_. O objetivo do `/grind` é que você seja um resolvedor autônomo.
