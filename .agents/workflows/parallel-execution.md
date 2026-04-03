---
description: Protocolo de execução paralela INNFLOW. Define como disparar múltiplos sub-agentes simultaneamente para tasks independentes. Reduz tempo de execução de planos complexos em 60-70%.
---

# ⚡ Parallel Execution — Sub-Agentes Simultâneos

## PRINCÍPIO FUNDAMENTAL

Tasks independentes **NUNCA devem esperar** tasks que não as bloqueiam.
Cada minuto de espera desnecessária é desperdício de tokens e tempo.

---

## IDENTIFICAR TASKS PARALELIZÁVEIS

Uma task é paralelizável quando:

- ✅ Não depende do output de outra task em andamento
- ✅ Modifica arquivos diferentes das outras tasks em paralelo
- ✅ Não altera schema de banco ou tipos compartilhados

Uma task NÃO é paralelizável quando:

- ❌ Depende de um arquivo sendo modificado por outra task
- ❌ Precisa de tipos/interfaces gerados por task anterior
- ❌ Envolve migration de banco (sempre sequencial)

---

## MAPA VISUAL OBRIGATÓRIO

Antes de executar, sempre gerar o mapa de dependências:

```
BLOCO A (Paralelo — dispara imediatamente)
  ├── [1.1] Design Tokens        → arquivo: tailwind.config.ts
  └── [1.2] Base CSS Animations  → arquivo: index.css
           │
           ▼ (aguardar BLOCO A completo)
BLOCO B (Paralelo — dispara após A)
  ├── [2.1] Sidebar Refactor     → arquivo: Sidebar.tsx
  ├── [2.2] Topbar Refactor      → arquivo: Topbar.tsx
  ├── [2.3] Dashboard Grid       → arquivo: DashboardLayout.tsx
  └── [3.1] Chart Theme          → arquivo: chart-theme.ts
           │
           ▼ (aguardar BLOCO B completo)
BLOCO C (Sequencial — caminho crítico)
  └── [5.1] Compose Pages        → múltiplos arquivos de page
```

---

## PROTOCOLO DE DISPARO PARALELO

### Para o Orquestrador (Antigravity)

Ao identificar um bloco paralelo no `parallel_blocks`, use ferramentas simultâneas:

```
INSTRUÇÃO INTERNA: Disparar as seguintes tasks em paralelo
(invocar ferramentas sem esperar resposta de cada uma):

- Task 2.1: [instrução completa e auto-suficiente]
- Task 2.2: [instrução completa e auto-suficiente]
- Task 2.3: [instrução completa e auto-suficiente]
```

**Cada instrução de sub-agente deve ser 100% auto-suficiente:**

- Qual arquivo modificar
- O que exatamente fazer
- Como validar que funcionou
- O que fazer se falhar

### Instrução Template para Sub-Agente

```markdown
## 🤖 SUB-AGENTE: [NOME DA TASK]

**ID:** [task_id]  
**Agente:** [tipo]  
**Arquivos:** [lista exata]  
**Dependências disponíveis:** [o que já foi concluído]

### O que fazer:

[Instrução específica e completa]

### Validação:

[Como confirmar que funcionou — comando ou checagem]

### Em caso de falha:

[O que fazer autonomamente antes de escalar]

### Output esperado:

[O que reportar ao Orquestrador ao concluir]
```

---

## SINCRONIZAÇÃO (BARRIER PATTERN)

Após disparar um bloco paralelo, o Orquestrador deve esperar confirmação de **todas** as tasks antes de avançar:

```markdown
⏳ AGUARDANDO BLOCO B:

- [2.1] Sidebar Refactor → 🔄 em andamento
- [2.2] Topbar Refactor → 🔄 em andamento
- [2.3] Dashboard Grid → 🔄 em andamento
- [3.1] Chart Theme → 🔄 em andamento

Avançar para BLOCO C apenas quando todos mostrarem ✅
```

---

## GESTÃO DE CONFLITOS

Se duas tasks paralelas modificaram o mesmo arquivo (erro de planejamento):

1. Identificar qual veio primeiro pelo git log
2. Aplicar manualmente o merge dos dois diffs
3. Registrar no scratchpad: "conflito entre [task A] e [task B], resolvido manualmente"
4. Ajustar o `decompose-task` para evitar sobreposição futura

---

## MÉTRICAS DE GANHO

| Estratégia                   | Tasks                | Tempo estimado       |
| ---------------------------- | -------------------- | -------------------- |
| Totalmente sequencial        | 10 tasks × 30min     | 5h                   |
| Com paralelização (3 blocos) | 10 tasks em 4 blocos | ~2h                  |
| **Ganho típico**             |                      | **~60% mais rápido** |
