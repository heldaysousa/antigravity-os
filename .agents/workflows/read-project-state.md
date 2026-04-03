---
description: Protocolo de leitura real-time do estado do projeto Antigravity OS. Fonte de verdade são os ARQUIVOS, não relatórios.
---

# Protocolo de Leitura Real-Time

## Fonte de Verdade

1. **Arquivos no filesystem** (código como está agora)
2. **Commits recentes** (últimas mudanças)
3. **Scratchpad** (tasks em andamento)

**Relatórios NÃO são fonte de verdade.** Servem apenas para contexto permanente (stack, decisões, regras).

## Sequência de Leitura (ao iniciar sessão)

### Etapa 1 — Estado das Tasks

```bash
# Ver scratchpad (se existir)
cat .agents/scratchpad.md 2>/dev/null || echo "Scratchpad não encontrado"
```

### Etapa 2 — Últimas Mudanças

// turbo

```bash
git log --oneline -5
```

### Etapa 3 — Arquivos Críticos

Ler apenas os que mais mudam entre sessões:

**Billing/Pagamento:**

- `supabase/functions/asaas-*/index.ts`
- Última migration em `supabase/migrations/`

**Frontend:**

- `apps/client/src/App.tsx` (rotas)
- Páginas novas/modificadas

**Hooks/Realtime:**

- `apps/client/src/hooks/useRealtime*.ts`

### Etapa 4 — Classificar Estado

Para cada arquivo crítico:

- ✅ **EXISTE E IMPLEMENTADO** — funcional
- 🔨 **EXISTE MAS INCOMPLETO** — process.env, TODO no código
- ❌ **NÃO EXISTE** — task não iniciada

### Etapa 5 — Verificar Pendências

```bash
# Procurar process.env em Edge Functions (deve ser Deno.env.get)
grep -r "process.env" supabase/functions/ 2>/dev/null

# Procurar TODO/FIXME
grep -r "TODO\|FIXME\|HACK" apps/ supabase/ --include="*.ts" --include="*.tsx" 2>/dev/null | head -20
```

## O que NÃO ler (economia de tokens)

- ❌ Relatórios .md completos
- ❌ Arquivos que não mudaram
- ❌ node_modules, .git, pnpm-lock.yaml, dist/
- ✅ APENAS arquivos das tasks ativas

## Mapa de Ações

| O que preciso saber | O que fazer               |
| ------------------- | ------------------------- |
| Estado das tasks    | Ler scratchpad            |
| O que mudou         | `git log --oneline -5`    |
| Arquivo existe?     | `ls -la [path]`           |
| Migration aplicada? | `supabase migration list` |
| Deploy status?      | `vercel ls`               |
| Edge Function OK?   | `supabase functions list` |

## Formato do Briefing

```markdown
## 🧠 BRIEFING — [DATA]

### FONTE DOS DADOS

- Scratchpad: [lido / ausente]
- Último commit: [sha] — [mensagem]
- Arquivos lidos: [lista]

### ESTADO DAS TASKS

T01 ... → ✅/🔨/❌
T02 ... → ✅/🔨/❌

### PRÓXIMOS PASSOS

1. [Próxima ação]
2. [Seguinte]
```
