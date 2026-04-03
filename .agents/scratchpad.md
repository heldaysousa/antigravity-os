# 📊 SCRATCHPAD MESTRE — REFATORAÇÃO GLOBAL UX/UI INNFLOW

## Status: 🔄 IN PROGRESS

## Progress: 14/22 (63%)

## Data Inicio: 2026-02-25

---

### 🔥 FASE 1: FUNDAÇÃO GLOBAL [Block A]

- ✅ 1.1: `tailwind.config.ts` (Tokens + Fonts) [@front-end-dev]
- ✅ 1.2: `index.css` (Variáveis, Animações, Limpeza Fragmentos) [@front-end-dev]
- ✅ 1.3: Componentes Core DS (Atoms) [@front-end-dev + @ui-ux-designer]

### 🏗️ FASE 2: LAYOUT SHELL [Block B]

- ✅ 2.1: `Sidebar.tsx` Refactor (64px, Icones) [@layout-engineer]
- ✅ 2.2: `Topbar.tsx` Refactor (Glass, Tabs) [@layout-engineer]
- ✅ 2.3: `DashboardLayout.tsx` (Grid 1fr + Ambient glow) [@layout-engineer]

### ✨ FASE 3: PÁGINAS CRÍTICAS + CHARTS [Block C]

- ✅ 3.1: Configuração Tema Recharts (`chart-theme.ts`) [@chart-specialist]
- ✅ 3.2: Overview (Rewrite Dashboard Hero/Charts) [@page-composer]
- ⬜ 3.3: Automação (Empty States, Glass Cards) [@page-composer]
- ⬜ 3.4: Suporte e Base de Conhecimento (Premium UI) [@page-composer]

### ⚡ FASE 4: ANIMAÇÕES E INTERAÇÕES [Block D]

- ✅ 4.1: CountUp nos KPIs [@animation-specialist]
- ✅ 4.2: DrawLine nas charts (stroke-dashoffset) [@animation-specialist]
- ⬜ 4.3: Stagger Layout (Classes .innflow-stagger) [@animation-specialist]
- ⬜ 4.4: Micro-Interações de Hover (-2px e border brighten) [@animation-specialist]

### 📦 FASE 5: MEDIUM & BATCH PAGES (Refinar Todas) [Block E]

- ✅ 5.1: Conversas (Chat Panel + Status Badges) [@page-composer]
- ✅ 5.2: Kanban (Pillars, Drag Ghost items) [@page-composer]
- ✅ 5.3: CRM Table (`.innflow-table`, Cells específicas) [@page-composer]
- ✅ 5.4: Configurações, Time e Treinar IA (Ajustes Finais Glass) [@page-composer]

### 🚧 FASE 6: VALIDAÇÃO & QA FINAL [Block F]

- ⬜ 6.1: Validação Visual WCAG Contrast e Reduced-Motion [@verifier]
- ⬜ 6.2: Build Check e TSC Strict Mode [@performance-analyzer]
- ⬜ 6.3: Deploy Vercel (Push e Status Check) [@deployment-engineer]

---

**Ultima Ação:** Criado Scratchpad Mestre para rastreio do Orchestrator.


===

# 🌟 CONTEXTO MERGEADO (DA SESSÃO RECENTE)

# 📊 SCRATCHPAD — INJEÇÃO DS v3.0 LIQUID GLASS

## Status: ✅ FASE 1 DONE + ✅ FASE 2 DONE

## Orquestrador: Antigravity (Gemini)

## Data: 2026-02-27T06:49 (BRT)

## Regra de Ouro: Orquestrador NÃO escreve código.

---

## 📌 CONTEXTO HERDADO

O projeto já completou 14/22 tasks do refactor anterior (ver `.agents/scratchpad.md`).
Fases 1-2 (fundação, layout shell) estão ✅ DONE.
Esta missão é uma EXTENSÃO: injetar tokens visuais do Design System v3.0 "Liquid Glass"
exportado do v0 (`_referencia_v0/`) no ecossistema Vite existente (`apps/client/`).

### Regras do DS INNFLOW (extraídas dos workflows):

- `body { background: #000000; }` — Pure Black, NUNCA cinza
- `--accent-primary: #19F58C` (155 94% 54%)
- Glass: `rgba(255,255,255,0.03)` bg + `rgba(255,255,255,0.06)` border + `blur(20px)`
- Dark mode ONLY (zero light mode em componentes)
- Grid 4px spacing
- TypeScript strict, zero `any`
- Zustand + useShallow para state

---

## 🗺️ FASE 1: FUNDAÇÃO VISUAL — 4 Tasks

### Task 1.1 — Mesclar Tailwind Config

- **Agente:** @frontend-developer
- **Ficheiro:** `apps/client/tailwind.config.ts`
- **Operação:** MERGE no `theme.extend` existente
- **ADICIONAR:**
  - `colors`: `mint`, `coral`, `royal` (tokens diretos — `gold`/`cyan`/`violet` já existem em `innflow.*`)
  - `colors.chart`: 1-5 HSL vars
  - `keyframes`: `float`, `pulse-soft`, `breathe`, `slide-up`, `fade-in`, `marquee`, `spin-slow` (nomes puros, sem prefix)
  - `animation`: mapeamentos correspondentes
  - `safelist`: adicionar `glass`, `glass-elevated`, `glass-subtle`, `btn-glow`, `btn-ghost`
- **NÃO TOCAR:**
  - `...baseConfig` spread (linhas 5-15)
  - `content` paths Vite (linhas 7-11)
  - Todos os blocos `innflow-*` e `tahoe-*` existentes
  - safelist existente (append only)
- **Validação:** `tsc --noEmit` no ficheiro
- **Status:** ✅ DONE

### Task 1.2 — Injetar Classes Premium no CSS Global

- **Agente:** @frontend-developer + @ui-ux-designer
- **Ficheiro:** `apps/client/src/index.css`
- **Operação:** APPEND após linha 842 (final do ficheiro)
- **ADICIONAR (novo @layer components):**
  - `.glass` (blur 40px saturate 1.5 — premium, diferente do `.glass-card` existente)
  - `.glass-elevated` (blur 60px, shadow pesada)
  - `.glass-subtle` (blur 20px, suave)
  - `.btn-glow` (botão mint com glow)
  - `.btn-ghost` (transparente com borda)
  - `.glow-mint`, `.glow-red`
  - `.label-tag` (11px uppercase tracking)
  - `.section-divider`, `.section-divider-glow`
  - `.whatsapp-bubble`, `.whatsapp-bubble-user`
- **ADICIONAR (keyframes standalone):**
  - `gradient-border` + `.gradient-border-animated::before`
  - `shine-sweep` + `.shine-sweep::after`
  - `text-shimmer` + `.text-gradient-animate`
  - `marquee`, `glow-pulse`, `float-particle`
  - `breathe`, `spin-slow`, `fade-in-up`, `typing`, `blink`, `count-up`
- **ADICIONAR (utilitários):**
  - `::selection { background: rgba(25,245,140,0.2); color: #fff; }`
- **NÃO TOCAR:**
  - `:root` tokens existentes (linhas 7-64)
  - `@layer base` existente (linhas 68-285)
  - Classes existentes (.glass-card, .btn-primary, etc.)
  - Imports `@tailwind` (linhas 1-4)
- **Validação:** Classes disponíveis no build
- **Status:** ✅ DONE

### Task 1.3 — Verificar Fonte Red Hat Display

- **Agente:** @frontend-developer
- **Ficheiro:** `apps/client/index.html`
- **Análise:**
  - ✅ Já importa `Red Hat Display:wght@300;400;500;600;700`
  - ✅ `preconnect` fonts.googleapis.com presente
  - ⚠️ Verificar se v0 usa peso 800/900 → se sim, add à query string
- **Ação provável:** SKIP (ou micro-update na query string)
- **Status:** ✅ DONE

### Task 1.4 — Validação Build

- **Agente:** @verifier
- **Comandos:**
  1. `pnpm --filter client tsc --noEmit`
  2. `pnpm --filter client build`
- **Critério:** Exit code 0 em ambos
- **Fallback:** Se FAIL → delegar fix ao @frontend-developer (até 3 retries — `/grind`)
- **Status:** 🔴 FAILED (Erros pré-existentes no Type-Check. Aguardando decisão de Grind)

---

## 📐 DEPENDÊNCIAS

```
Task 1.1 (Tailwind) ───┐
Task 1.2 (CSS)      ───┼──→ Task 1.4 (Build Validation)
Task 1.3 (Font)     ───┘

Parallel Block: [1.1, 1.2, 1.3]
Sequential:     [1.4] (depende de TODAS acima)
```

---

## 🚫 ANTI-PATTERNS

- ❌ Copiar tailwind.config.ts inteiro do v0 (quebraria baseConfig/paths Vite)
- ❌ Copiar layout.tsx ou next.config.mjs (Next.js-specific)
- ❌ Substituir :root vars existentes (quebraria components DS v2.0)
- ❌ Remover classes innflow-_/tahoe-_ existentes
- ❌ Mudar body background (já é #000000 — Regra de Ouro)
- ❌ Usar `process.env` (apenas `import.meta.env` para Vite)
