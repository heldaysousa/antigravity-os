---
description: Decomposição analítica de tarefas complexas em sub-tasks com agentes, dependências e paralelização. Baseado no splitter-agent Antigravity OS.
---

# Decomposição Analítica de Tarefas

## Quando Usar

- Tarefas complexas com múltiplos arquivos/camadas
- Implementações que envolvem frontend + backend + database
- Features que requerem coordenação entre múltiplos domínios

## Procedimento

### 1. Analisar a Tarefa

Identifique:

- Quantos domínios estão envolvidos (frontend, backend, database, deploy, etc.)
- Quais arquivos serão criados/modificados
- Quais são as dependências entre sub-tasks

### 2. Gerar JSON Estruturado

```json
{
  "requires_multiple_agents": true,
  "agents_needed": ["frontend", "backend", "database"],
  "execution_order": [
    {
      "task_id": "1.1",
      "name": "Nome da task",
      "agent": "agent-type",
      "reason": "Por que este agente",
      "focus": "O que focar",
      "files": ["path/to/file1", "path/to/file2"],
      "dependencies": [],
      "duration_estimate": "15min",
      "validation_strategy": "Como validar"
    }
  ],
  "parallel_blocks": [{ "tasks": ["1.1", "1.2"], "reason": "Independentes" }],
  "critical_path": ["1.1", "1.3", "1.5"],
  "estimated_duration": "2h",
  "risk_assessment": "Riscos identificados"
}
```

### 3. Visualização ASCII

```
FASE 1: Fundação
  ├── [1.1] Design Tokens ──────┐
  ├── [1.2] Tailwind Config ────┤ PARALELO
  └── [1.3] Componente Base ────┘
                                │
FASE 2: Construção              ▼
  ├── [2.1] Componente A ───────┐
  └── [2.2] Componente B ───────┤ PARALELO (depende de 1.x)
                                │
FASE 3: Integração              ▼
  └── [3.1] Integrar tudo ──── SEQUENCIAL
```

### 4. Regras de Decomposição

| Padrão                           | Estratégia                                         |
| -------------------------------- | -------------------------------------------------- |
| Full-stack                       | Sequencial: DB → Backend → Frontend                |
| Múltiplas features independentes | Paralelo                                           |
| Refatoração + Testes             | Sequencial: refatorar → testar                     |
| Code + Review                    | Sequencial: implementar → revisar                  |
| Deploy                           | Sequencial: migrations → edge functions → frontend |

### 5. Entregar ao Usuário

- Lista clara de tasks com estimativas
- Identificar o que pode rodar em paralelo
- Identificar o critical path (caminho mais longo)
- Apontar riscos e mitigações
