---
description: Protocolo de economia de tokens Antigravity OS. Ativar quando contexto estiver grande ou task for complexa (>10 arquivos). Previne overflow e degradação de qualidade.
---

# 💰 Token Budget — Protocolo Anti-Overflow

## QUANDO ATIVAR

- Sessão com >15 arquivos abertos no contexto
- Orquestração com >10 tasks
- Após 3+ trocas longas com o usuário
- Modelo retornar erro `MODEL_CAPACITY_EXHAUSTED`

---

## REGRAS DE OURO

### 1. Não Carregar o que Não Vai Usar

```
❌ ERRADO: Ler 10 arquivos "para ter contexto geral"
✅ CERTO:  Ler APENAS os arquivos da task atual
```

### 2. Hierarquia de Leitura (do menor para o maior custo)

| Ferramenta                        | Custo    | Quando usar                          |
| --------------------------------- | -------- | ------------------------------------ |
| `grep_search`                     | ⚡ Baixo | Checar se algo existe antes de abrir |
| `view_file_outline`               | ⚡ Baixo | Entender estrutura sem ler tudo      |
| `view_file` com StartLine/EndLine | 🟡 Médio | Ler seção específica                 |
| `view_file` completo              | 🔴 Alto  | Apenas quando essencial              |

### 3. Resumo em vez de Citação

Nunca reproduza blocos longos de código no chat. Use:

- "Arquivo X atualizado com Y" — não colar o arquivo inteiro
- "Erro na linha 45: tipo incorreto" — não colar o stack completo

### 4. Scratchpad como Memória Externa

Ao invés de re-explicar o contexto passou, referencie o scratchpad:

```bash
cat .agents/scratchpad.md
```

Atualizar scratchpad a cada task concluída elimina a necessidade de re-contextualizar.

---

## PROTOCOLO DE COMPRESSÃO DE CONTEXTO

Se a sessão estiver "pesada" demais, aplicar este reset:

### Passo 1 — Atualizar Scratchpad

Registrar estado atual de TODAS as tasks no scratchpad antes de qualquer coisa.

### Passo 2 — Identificar o Próximo Passo

Só ler os arquivos necessários para a PRÓXIMA task. Nada mais.

### Passo 3 — Usar Referências Incrementais

```markdown
**Status:** Task 3.2 concluída. Próxima: Task 3.3.
**Bloqueios:** Nenhum.
**Arquivos afetados:** AnimatedAreaChart.tsx
```

### Passo 4 — Declarar Budget Explícito

```
BUDGET: Esta task mexe em APENAS 2 arquivos:
- apps/client/src/pages/Kanban.tsx
- apps/client/src/components/ui/ds/GlassCard.tsx
Fora destes, NÃO ler outros arquivos.
```

---

## ANTI-PATTERNS DE TOKENS

- ❌ Reproduzir arquivos inteiros no chat para "confirmar"
- ❌ Re-ler arquivos que não mudaram desde a última leitura
- ❌ Pedir `view_file` em node_modules, .git, dist, pnpm-lock.yaml
- ❌ Incluir contexto de sessões anteriores manualmente
- ❌ Escrever respostas com >500 tokens quando 100 bastam

---

## CHECKLIST PRÉ-TASK

- [ ] Li APENAS os arquivos necessários para esta task?
- [ ] Usei `grep_search` antes de `view_file` para localizar o que preciso?
- [ ] O scratchpad está atualizado com o estado atual?
- [ ] Minha resposta será concisa e sem repetição?
