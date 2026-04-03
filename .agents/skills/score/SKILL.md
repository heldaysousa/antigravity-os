---
name: Lead Score Engine
description: Algoritmo termal para cálculo de Lead Score no Innflow Engine (Grupo C).
---

# Lead Score Skill v2 (Temperatura do Lead)

O **Lead Score Engine** é chamado na Etapa Crítica (Grupo C).
Ele decide a temperatura e se o BOT deve repassar para um atendente humano imediatamente.

## Níveis de Temperatura
1. **COLD (< 50pts):** Sem intenção clara, respostas monossilábicas. (Retenção 100% no BOT)
2. **WARM (50 - 79pts):** Engajado, respondendo o BOT ou com intenção geral de serviço/dúvida.
3. **HOT (80 - 89pts):** Intenção forte de contratação, perguntou preços, validou features.
4. **BURNING (90+ pts):** Cliente pronto para pagar. Se ele enviar a palavra "pagar", "Link", "Onde clico", "Comprar".

## Ações base
- Se `BURNING` e não houver resposta em > 5 minutos = Dashboard alerta "URGENTE".
- Se `HOT` ou `BURNING` ou Emotion=`angry` = O Engine automaticamente dispara trigger para encaminhar para atendente humano na plataforma.
