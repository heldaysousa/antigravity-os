---
description: Padrões de testes Antigravity OS — Vitest para unit, pgTAP para database, Playwright para E2E. Rodar antes de qualquer merge/deploy.
---

# Testes Antigravity OS

## Unit Tests (Vitest)

**Localização:** `apps/client/src/**/*.test.{ts,tsx}`

```ts
import { describe, it, expect } from 'vitest';
import { render, screen } from '@testing-library/react';
import { ComponentName } from './ComponentName';

describe('ComponentName', () => {
  it('renders correctly', () => {
    render(<ComponentName title="Test" />);
    expect(screen.getByText('Test')).toBeInTheDocument();
  });
});
```

### Comandos

// turbo

```bash
pnpm test
```

// turbo

```bash
pnpm test --coverage
```

## pgTAP Tests (Database)

**Localização:** `supabase/tests/database/*.test.sql`

```sql
BEGIN;
SELECT plan(2);

SELECT has_table('public', 'conversations', 'conversations table exists');
SELECT row_level_security_enabled('public', 'conversations', 'RLS enabled');

SELECT * FROM finish();
ROLLBACK;
```

### Comandos

// turbo

```bash
supabase test db
```

## E2E Tests (Playwright)

**Localização:** `tests/e2e/*.test.ts`

```ts
import { test, expect } from "@playwright/test";

test("login flow", async ({ page }) => {
  await page.goto("/login");
  await page.fill('[name="email"]', "test@example.com");
  await page.fill('[name="password"]', "password");
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL(/dashboard/);
});
```

### Comandos

```bash
npx playwright test
npx playwright test --ui
npx playwright show-report
```

## Migration Tests

// turbo

```bash
supabase db lint
```

// turbo

```bash
supabase db diff
```

```bash
supabase db reset
```

## Checklist Pré-Deploy

- [ ] Unit tests passando (`pnpm test`)
- [ ] TypeScript sem erros (`pnpm type-check`)
- [ ] pgTAP: RLS e funções validadas
- [ ] E2E: fluxos críticos cobertos
- [ ] Build OK (`pnpm build`)
