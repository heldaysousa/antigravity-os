---
description: Criar componente React seguindo padrões Antigravity OS. Functional component, TypeScript strict, Tailwind dark-first, Zustand, React Query.
---

# Criar Componente React (Padrões Antigravity OS)

## Template Base

```tsx
import { cn } from "@/lib/utils";

interface ComponentNameProps {
  title: string;
  onAction?: () => void;
  variant?: "default" | "elevated" | "interactive";
  size?: "sm" | "md" | "lg";
  children?: React.ReactNode;
  className?: string;
}

export function ComponentName({
  title,
  onAction,
  variant = "default",
  size = "md",
  children,
  className,
}: ComponentNameProps) {
  // Hooks primeiro
  // Lógica depois
  // Render por último

  return (
    <div className={cn("component-base-class", className)}>{children}</div>
  );
}
```

## Regras Obrigatórias

### TypeScript

- Zero `any` — TypeScript strict mode
- Props tipadas com interface
- Export nomeado (nunca default)
- Valores default no destructuring

### Hooks

- **Zustand + useShallow** para UI state
- **React Query** (TanStack) para server state
- Não usar Context API para state

```tsx
import { useShallow } from "zustand/react/shallow";

const { period, setPeriod } = useAnalyticsStore(
  useShallow((state) => ({ period: state.period, setPeriod: state.setPeriod })),
);
```

### Styling (Tailwind)

- Dark mode ONLY (zero light mode)
- Design System tokens via CSS variables
- Glassmorphism: `bg-white/[0.03] backdrop-blur-xl border border-white/[0.06]`

### Acessibilidade

- Semantic HTML
- ARIA quando necessário
- keyboard navigation (Tab, Enter, Escape)
- `focus-visible` states

## Design System Tokens

```css
/* Cores */
--accent-primary: #19f58c; /* 155 94% 54% */
--bg-app: #000000;
--bg-card: #0a0a0c;
--text-primary: #ffffff;
--text-secondary: rgba(255, 255, 255, 0.5);

/* Glassmorphism */
--glass-bg: rgba(255, 255, 255, 0.03);
--glass-border: rgba(255, 255, 255, 0.06);
--glass-blur: blur(20px) saturate(1.3);

/* Spacing (grid 4px) */
--space-1: 0.25rem;
--space-2: 0.5rem;
--space-3: 0.75rem;
--space-4: 1rem;
--space-6: 1.5rem;
--space-8: 2rem;

/* Radius */
--radius-sm: 8px;
--radius-md: 12px;
--radius-lg: 16px;
--radius-xl: 20px;

/* Transitions */
--duration-fast: 150ms;
--duration-normal: 250ms;
--ease-out: cubic-bezier(0, 0, 0.2, 1);
```

## Checklist

- [ ] Functional component com props tipadas
- [ ] TypeScript strict (zero any)
- [ ] Tailwind classes (dark mode)
- [ ] Zustand + useShallow para state
- [ ] Accessibility (semantic HTML, ARIA)
- [ ] Hover/focus states
- [ ] Responsive (sm/md/lg breakpoints)
