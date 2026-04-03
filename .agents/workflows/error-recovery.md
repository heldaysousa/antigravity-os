---
description: Protocolo de recuperação autônoma de erros Antigravity OS. Define como o agente deve agir em caso de falha em qualquer task — retry, escalação, rollback. Nunca travar, nunca desistir sem documentar.
---

# 🚑 Error Recovery — Recuperação Autônoma

## ÁRVORE DE DECISÃO APÓS FALHA

```
Task falhou?
    │
    ▼
É erro de TypeScript/Lint?
    ├── SIM → Ativar /grind (até 3 tentativas auto-correção)
    └── NÃO → continuar abaixo
    │
    ▼
É erro de build/import?
    ├── SIM → grep_search para localizar import quebrado → corrigir → re-build
    └── NÃO → continuar abaixo
    │
    ▼
É erro de Edge Function / Supabase?
    ├── SIM → Ativar /debug (diagnóstico de causa raiz)
    └── NÃO → continuar abaixo
    │
    ▼
É erro de serviço externo (503, timeout, API)?
    ├── SIM → Aguardar 30s → retry até 2x → documentar no scratchpad
    └── NÃO → Escalar para usuário com diagnóstico completo
```

---

## NÍVEIS DE RETRY

### Nível 1 — Auto-Fix Silencioso (0 interrupções)

**Quando:** Erros de TypeScript, ESLint, imports quebrados  
**Ação:** Corrigir diretamente usando `multi_replace_file_content` sem avisar o usuário  
**Limite:** 3 tentativas  
**Reportar:** Apenas ao final, via relatório ASCII

### Nível 2 — Auto-Fix com Notificação (1 aviso)

**Quando:** Erro de lógica que requer mudança de abordagem  
**Ação:** Notificar o usuário da mudança de estratégia, corrigir e continuar  
**Template:**

```
⚠️ Estratégia ajustada: [razão] → [nova abordagem]. Continuando...
```

### Nível 3 — Bloqueio para Aprovação

**Quando:** Erro que exige decisão humana (conflito de mer, schema breaking change, segurança)  
**Ação:** PARAR, documentar o estado no scratchpad, apresentar opções claras:

```markdown
## 🛑 BLOQUEIO — Decisão Necessária

**Problema:** [Descrição]
**Contexto:** [O que foi tentado]
**Opções:**
A) [Opção conservadora] — Impacto: baixo, tempo +Xmin
B) [Opção agressiva] — Impacto: alto, tempo +Ymin
C) [Rollback] — Voltar ao commit [sha]

**Minha recomendação:** [A/B/C] porque [razão]
```

---

## ROLLBACK AUTOMÁTICO

Se o estado do código piorou após tentativas de fix:

// turbo

```bash
git stash
```

Documentar no scratchpad o que foi revertido e por quê.

---

## PROTOCOLO DE FALHA CATASTRÓFICA

Se build quebrou completamente e não há fix óbvio:

1. `git stash` — salvar estado atual
2. `git log --oneline -5` — identificar último commit estável
3. `git checkout [sha-estável]` — voltar ao ponto seguro
4. Documentar no scratchpad: o que estava tentando, o que quebrou
5. Apresentar ao usuário com contexto completo

---

## O QUE NUNCA FAZER

- ❌ Dizer "não consigo corrigir" sem tentar pelo menos 2 abordagens diferentes
- ❌ Deixar o projeto em estado quebrado sem fazer git stash
- ❌ Continuar após falha catastrófica sem rollback
- ❌ Pedir ao usuário para corrigir o que é responsabilidade do agente
