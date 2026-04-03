---
description: Protocolo de feature flags INNFLOW. Implementar features novas de forma segura com toggle ON/OFF sem precisar de deploy separado. Ideal para features de risco médio-alto.
---

# 🚦 Feature Flag — Deploy Seguro de Features

## QUANDO USAR

- Feature complexa que leva múltiplas sessões
- Feature que pode ser ativada/desativada por cliente
- Teste A/B de UI
- Release controlado para subconjunto de usuários

---

## IMPLEMENTAÇÃO PADRÃO INNFLOW

### 1. Criar a Constante de Flag

```typescript
// apps/client/src/lib/feature-flags.ts
export const FEATURE_FLAGS = {
  NEW_DASHBOARD_V2: import.meta.env.VITE_FLAG_DASHBOARD_V2 === "true",
  KANBAN_DRAG_V2: import.meta.env.VITE_FLAG_KANBAN_V2 === "true",
  CRM_AI_SUGGEST: import.meta.env.VITE_FLAG_CRM_AI === "true",
} as const;

export type FeatureFlag = keyof typeof FEATURE_FLAGS;
```

### 2. Hook de Verificação

```typescript
// apps/client/src/hooks/useFeatureFlag.ts
import { FEATURE_FLAGS, FeatureFlag } from "@/lib/feature-flags";

export function useFeatureFlag(flag: FeatureFlag): boolean {
  return FEATURE_FLAGS[flag];
}
```

### 3. Uso nos Componentes

```tsx
import { useFeatureFlag } from "@/hooks/useFeatureFlag";

function Dashboard() {
  const hasNewDashboard = useFeatureFlag("NEW_DASHBOARD_V2");

  return hasNewDashboard ? <DashboardV2 /> : <DashboardLegacy />;
}
```

### 4. Variáveis de Ambiente

```bash
# .env.development
VITE_FLAG_DASHBOARD_V2=true   # Ativo em dev

# .env.production
VITE_FLAG_DASHBOARD_V2=false  # Desativado em prod até aprovação
```

---

## CICLO DE VIDA DA FLAG

```
CRIAÇÃO → DESENVOLVIMENTO → TESTE → ATIVAÇÃO → REMOÇÃO
  (off)       (off/on)     (on stg)  (on prod)   (código limpo)
```

**Regra:** Flags com >30 dias ativas em produção devem ser **removidas** (o código legado também). Criar task no scratchpad para isso.

---

## CHECKLIST

- [ ] Flag criada em `feature-flags.ts`
- [ ] Hook `useFeatureFlag` utilizado (nunca acesso direto)
- [ ] `.env.example` atualizado com nova variável
- [ ] `.env.development` e `.env.production` atualizados
- [ ] Componente legado mantido até remoção da flag
- [ ] Task de remoção futura documentada no scratchpad
