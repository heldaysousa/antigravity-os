# Prompts — Fase 3: Liquid Glass Refactor

## TASK 3.1: Settings Tabs

**Metadata:**

- Agent: @front-end-dev
- Priority: High
- Duration: 20m

**Validation Criteria:**

- `grep -r 'bg-gray'` retorna 0 para os ficheiros em causa.
- `pnpm run type-check` sem erros novos.

**The Prompt:**

```text
ROLE: @front-end-dev
MISSION: Remover tokens antigos do Tailwind (bg-gray, border-gray, text-gray) nas tabs de Settings e substituí-los pela infraestrutura DS v3.0 ("Liquid Glass").

REQUIREMENTS:
1. Modifique os ficheiros em 'src/pages/dashboard/components/settings/'.
2. Substitua bg-gray-800/50 por "bg-white/[0.04] backdrop-blur-md border border-white/[0.08] rounded-xl".
3. Substitua Inputs por <InputGlass /> (importe de @/components/ui/ds).
4. Substitua Buttons principais por <ButtonGlass /> (importe de @/components/ui/ds).
5. Envolva containers de secção grandes com <GlassCard className="p-6">.

ANTI-PATTERNS:
- NÃO altere os hooks de chamada à DB ou à API (useUserRole, useSettings, etc).
- NÃO use cores estáticas #HEX que não sejam o Mint primário ou equivalentes do DS.
```

---

## TASK 3.2: Conversations + Chat Layer

**Metadata:**

- Agent: @ui-ux-designer + @front-end-dev
- Priority: High

**The Prompt:**

```text
ROLE: @ui-ux-designer
MISSION: Unificar a camada visual das conversas para Liquid Glass, removendo o cinza residual.

REQUIREMENTS:
1. Em 'ConversationList.tsx' e demais componentes da pasta, remover todo bg-gray.
2. Bubbles do usuário: "bg-white/[0.06] border border-white/[0.08] rounded-2xl rounded-tl-none".
3. Bubbles da IA: "bg-[#19F58C]/[0.12] border border-[#19F58C]/20 rounded-2xl rounded-tr-none".
4. Adicionar className="animate-pulse-soft text-[#19F58C]" no status de IA.
```

## TASK 3.3: Atoms / Base UI

**Metadata:**

- Agent: @front-end-dev
- Priority: Critical

**The Prompt:**

```text
ROLE: @front-end-dev
MISSION: Espelhar Atoms base para os layouts Liquid Glass, gerando cascata nas outras UI's.

REQUIREMENTS:
1. Atom Modal: overlay em backdrop-blur-sm, Modal central com .glass-panel-v3.
2. Atom Avatar: ring-white/10 e status pulse.
```
