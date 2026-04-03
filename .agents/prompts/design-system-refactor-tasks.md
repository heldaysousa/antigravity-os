# 📋 INNFLOW PROMPTS MESTRES — REFATORAÇÃO GLOBAL UX/UI

## 💡 SOBRE ESTE DOCUMENTO

Este arquivo foi gerado automaticamente pela FASE 2 do orchestrator. Ele contém as instruções táticas exatas (prompts) que o `@agent-orchestrator` irá alimentar para os subagentes (`@frontend-developer`, `@ui-ux-designer`, etc) ao longo da Fase 4.

---

### TASK 1.1 — Design Tokens Customization

**Metadata:** Agent: `@frontend-developer`, Priority: HIGHEST, Duration: 15m.
**The Prompt:**
"Aja como @frontend-developer.
Sua missão: Garantir a configuração canônica do INNFLOW Design System v2.0-Tahoe no seu motor de utilitários `tailwind.config.ts`.
**Requisitos:**

1. Validar se a tipografia padrão `Red Hat Display` está instanciada em `fontFamily.sans`.
2. Garantir as chaves de fallback para Dark Mode (`darkMode: 'class'`).
3. Assegurar que as cores `innflow-green`, `innflow-violet` etc. e o Apple Tahoe array (`cyan-vibrant`) permanecem não poluidos.
   **Anti-patterns:** Alterar lógicas do Prettier plugins contidos no final do tailwind.config. Mudar os tokens de root ShadCN (`hsl(var(--primary))`) já pré-existentes.
   **Success Criteria:** `pnpm tsc --noEmit` do arquivo tailwind passa perfeitamente."

---

### TASK 1.2 — Base CSS Fix + Animações

**Metadata:** Agent: `@frontend-developer`, Priority: HIGHEST, Duration: 25m.
**The Prompt:**
"Aja como @frontend-developer.
Sua missão: Limpar a casa e purificar o `apps/client/src/index.css`. Foram removidos os 14 fragments CSS soltos da pasta style e toda a herança precisa vir do root index.
**Requisitos:**

1. A base `body { background: #000000; }` deve ser absoluta. Cinza escuro é inaceitável. Na layer base/componentes.
2. Incorpore todos os `keyframes` listados na Spec do Apple Tahoe (stagger fade-in, draw-line, count-up glow).
3. Garantir a compatibilidade regressiva de variáveis ShadCN via CSS variables `:root`.
   **Validation:** Rodar rebuild Vite, assegurar visualização global no browser (background = black)."

---

### TASK 1.3 — Core Component Primitives

**Metadata:** Agent: `@ui-ux-designer` e `@frontend-developer`, Duration: 60m.
**The Prompt:**
"Aja como @ui-ux-designer com habilidades React.
Sua missão: Criar Prmitives Componentes do _Credit Dashboard_ em `apps/client/src/components/ui/ds`.
**Requisitos:**

1. Criar novo `GlassCard.tsx`: O coração do UI. Propriedade explícita `backdrop-filter: blur(12px)` e background `rgba(255,255,255,0.02)`. Border superior 1px stroke white 12% opacidade.
2. Criar `KpiNumber.tsx`: Fonte weight: 100 estrita (Thin), tracking-widest para números hero de KPI em Dashboard.
3. Criar `StatusBadge.tsx`: Pill oval com background green/violet 15% e border green/violet 30%.
   **Anti-patterns:** Uso de `px` indevido (use o grid de 4px de spacing do tailwind via pr-2, ml-4).
   **Success Criteria:** Todos os 3 exportados via `index.ts` corretamente."

---

### TASK 2.1 — Sidebar Refactor (64px)

**Metadata:** Agent: `@layout-engineer`, Duration: 40m.
**The Prompt:**
"Aja como @layout-engineer.
Sua missão: Destriuir a Sidebar legada expandida e criar a nova Sidebar compacta (Mobile-first, default = 64px width).
**Requisitos:**

1. Esconder labels de texto permanentemente. O menu será apenas de Ícones Apple Tahoe.
2. Hover state: o ícone deve sofrer scale(1.10) num `spring` duration 300ms.
3. Logo Topo: Apenas o Infinity sign `∞` verde pulsante, sem texto lateral.
   **Success Criteria:** Ao clicar num item de menu a rota `<Link react-router>` funciona normalmente, validando que navegação base não quebrou."

---

### PARALLEL BLOCK C (CHARTS & ANIMATIONS) // ATALHO

**Tasks Integradas:** 3.1, 3.2, 4.1
**Metadata:** Agent: `@chart-specialist` e `@animation-specialist`, Duration: 45m.
**The Prompt:**
"Aja como Especialista Híbrido Recharts e FramerMotion/CSS.
Sua missão: Fazer as Data Visualizations parecerem Premium e caras.
**Requisitos:**

1. Configurar arrays de cores em um arquivo helper `/lib/chart-theme.ts` (ex: `['#19F58C','#0066FF','#8F00FF']`).
2. Remover Tooltips nativos feios do Recharts. Injetar um Custom div React que seja um GlassCard minúsculo.
3. Desligar o background lines do Cartesion Grid do recharts para opacity = 0.04.
4. Quando os valores carregam, os números devem subir devagar de 0 até o valor real (CountUp animation requestAnimationFrame).
   **Validation:** Ver os gradients de fillSVG não causarem warnings no React console."

---

### TASK 5.1 — Dashboard Overview Assemble

**Metadata:** Agent: `@page-composer`, Priority: HIGH, Duration: 60m.
**The Prompt:**
"Aja como @page-composer.
Sua missão: Montar a tela principal de Overvidew e colar os blocos juntos.
**Requisitos:**

1. Você não pode criar estilos inline! Use os componentes `GlassCard`, `KpiNumber`, novo `Sidebar` criados anteriormente.
2. O Layout topo da página deve possuir a grid Row nativa do Design (4 colunas para telas Desktop, caindo pra 1-col no Mobile). Row: [ Conversão | Atendimentos | Receita Mês (Rainbow) | Rating ].
3. Enrolar a renderização list num container `<div className="innflow-stagger">` para cada card aparecer 1 de cada vez sucessivamente em cascata linear.
   **Anti-patterns:** Apagar a lógica dos React Contexts / Providers das páginas.
   **Success Criteria:** Dashboard renderiza vivo no Browser!"
