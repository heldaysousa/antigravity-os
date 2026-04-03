---
description: Protocolo de handoff entre sessões Antigravity OS. Usar ao iniciar nova sessão ou após interrupção longa. Garante continuidade sem re-explicar contexto do zero.
---

# 🤝 Context Handoff — Continuidade entre Sessões

## QUANDO USAR

- Início de nova sessão de trabalho
- Após fechamento e reabertura do IDE
- Após erro `MODEL_CAPACITY_EXHAUSTED` que forçou restart
- Troca de agente / contexto

---

## PROTOCOLO DE ENTRADA (ao iniciar sessão)

### Etapa 1 — Ler o Scratchpad Mestre

// turbo

```bash
cat .agents/scratchpad.md
```

### Etapa 2 — Ver Últimas Mudanças no Git

// turbo

```bash
git log --oneline -10
```

### Etapa 3 — Verificar Estado do Build

// turbo

```bash
pnpm run type-check 2>&1 | tail -20
```

### Etapa 4 — Montar o Briefing de Entrada

Após ler as 3 fontes acima, gerar automaticamente:

```markdown
## 🧠 BRIEFING — [DATA ATUAL]

### ESTADO DO PROJETO

- Progresso: X/Y tasks (Z%)
- Última task completa: [nome]
- Próxima task: [nome + ID]
- Bloqueios ativos: [lista ou "Nenhum"]

### SAÚDE DO CÓDIGO

- TypeScript: ✅ limpo / ❌ [N erros]
- Último commit: [sha] — [mensagem]

### PRÓXIMA AÇÃO

→ [Ação imediata e objetiva]
```

---

## PROTOCOLO DE SAÍDA (ao encerrar sessão)

Antes de fechar, SEMPRE atualizar o scratchpad com:

### Template de Encerramento

```markdown
## Última Sessão: [DATA]

### O que foi feito

- ✅ [Task] — [descrição rápida]
- ✅ [Task] — [descrição rápida]

### Estado atual

- 🔄 [Task em andamento] — parou em: [ponto exato]

### Próxima sessão deve começar em

→ [Instrução clara e objetiva para retomar]

### Arquivos modificados nesta sessão

- path/to/file1.tsx
- path/to/file2.ts
```

---

## ANTI-PATTERNS

- ❌ Começar a codar sem ler o scratchpad
- ❌ Pedir ao usuário para re-explicar o que já estava no scratchpad
- ❌ Deixar sessão sem atualizar o scratchpad ao encerrar
- ❌ Assumir que o contexto da sessão anterior ainda está disponível
