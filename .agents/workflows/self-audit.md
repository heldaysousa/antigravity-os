---
description: Protocolo de auto-auditoria Antigravity OS. O agente deve rodar este checklist antes de marcar qualquer task como ✅ concluída. Evita falsos positivos e retrabalho.
---

# 🔬 Self-Audit — Checagem antes de Declarar "Pronto"

## REGRA DE OURO

**Nenhuma task é marcada como ✅ sem passar pelos 5 filtros abaixo.**

Declarar "pronto" sem validar é a causa #1 de retrabalho e sessões longas.

---

## FILTRO 1 — O Arquivo Realmente Existe?

// turbo

```bash
ls -la [path/do/arquivo]
```

Confirmação mínima antes de qualquer outra verificação.

---

## FILTRO 2 — TypeScript Está Limpo?

// turbo

```bash
pnpm run type-check 2>&1 | tail -30
```

**PASS:** Exit code 0, zero erros  
**FAIL:** Qualquer erro → ativar `/grind`

---

## FILTRO 3 — Imports Estão Resolvidos?

```bash
grep -r "from '.*[task-specific-module]'" apps/client/src --include="*.tsx" --include="*.ts" | head -10
```

Verificar se o módulo criado/modificado está sendo importado corretamente em quem depende dele.

---

## FILTRO 4 — Nenhum TODO/FIXME Deixado para Trás?

```bash
grep -r "TODO\|FIXME\|HACK\|XXX" --include="*.ts" --include="*.tsx" [arquivos-modificados]
```

Se existir: ou resolver agora, ou documentar como task futura no scratchpad.

---

## FILTRO 5 — O Output Visual é o Esperado?

Para tasks de frontend, validar com `browser_subagent` quando aplicável:

- O componente renderiza sem erro visual?
- O layout não quebrou em nenhuma viewport?
- Animações funcionam?

---

## TEMPLATE DE REPORTE PÓS-TASK

Ao concluir uma task, sempre reportar neste formato:

```markdown
## ✅ Task [ID] Concluída: [Nome]

**Arquivos modificados:**

- `path/to/file1.tsx`

**O que foi feito:**
[1-3 frases objetivas]

**Validação:**

- TypeScript: ✅ 0 erros
- Build: ✅ ok
- Visual: ✅ [componente renderiza conforme esperado]

**Side-effects conhecidos:**

- [Outro arquivo que pode ser afetado mas não foi testado]

**Próxima task desbloqueada:**
→ [Task ID] — [Nome]
```

---

## ANTI-PATTERNS

- ❌ Dizer "deve funcionar" sem testar
- ❌ Marcar task como ✅ baseado apenas em "código parece certo"
- ❌ Pular type-check porque "são apenas mudanças pequenas"
- ❌ Não mencionar side-effects conhecidos
