# Web primitives — mixed library binding

The skill is stack-agnostic. This file maps behavior rules to concrete libraries for a Next.js + Tailwind v4 stack. Behavior checklists (in each `components/*.md`) stay source of truth; this file only says *which library provides the primitive we wire the behavior to*.

## Library choices

- **Primary:** Base UI 1.x (Operate's stack) — `@base-ui/react` on npm, stable at 1.4.0 as of Apr 2026.
- **Fallback for behavior-heavy:** react-aria headless hooks (`@react-aria/*`) — used where Base UI has no primitive and the a11y / keyboard contract is non-trivial to hand-roll.
- **Command palette:** `cmdk` (Paco Coursey) — first-class dependency.
- **Hand-rolled:** ListRow, Filter, StatusPill, EmptyState — compositions of the above plus custom semantics.

"First-class dependency" means cmdk is installed, imported, and used directly — we do not wrap it in an abstraction layer or try to recreate its scoring logic. Same stance as Linear: adopt the primitive, brand the surface. See `_decisions-ledger.md § 2026-04-17 item 1`.

Pick Base UI first for any component where it ships a primitive. Reach for react-aria only when (a) Base UI has no primitive (DatePicker is the headline case) OR (b) we need a lower-level hook because Base UI's rendering assumptions don't match the spec. Do not reach for react-aria to "add behavior polish" to a Base UI component — Base UI's behaviors are already grounded in the same ARIA authoring practices. Mixing produces two keyboard models.

Hand-rolled means exactly that: we own the markup, ARIA, keyboard handling, and focus. This is acceptable for the four components where primitives would over-constrain (ListRow, Filter, StatusPill, EmptyState) and the keyboard contract is small enough to manage in ~100 lines. The corresponding `components/*.md` files are the contract — no drift permitted.

## Per-component binding (the Common 15 + three v1.1 promotions)

Three components were promoted from v1.1 deferred to v0 on 2026-04-21 (Peek, Drawer, Tabs) — see `_decisions-ledger.md § 2026-04-21`. They are included in the table below.

| Component | Primary library | Key API | Notes |
|---|---|---|---|
| Button | Base UI `Button` | `<Button />` | Full |
| Input (inline-edit pattern) | Base UI `Input` | Wrap with inline-edit state machine | Pattern in `components/input-inline-edit.md` |
| Combobox | Base UI `Combobox` | `<Combobox.Root>` | Full — see `components/combobox-select.md` |
| Select | Base UI `Select` | `<Select.Root>` | Full — see `components/combobox-select.md` |
| DropdownMenu | Base UI `Menu` | `<Menu.Root>` + `ChangeEventReason` taxonomy | Full — see `components/dropdown-menu.md § ChangeEventReason` |
| Dialog | Base UI `Dialog` | `<Dialog.Root>` | Full; includes AlertDialog — see `components/dialog.md` |
| Popover | Base UI `Popover` | `<Popover.Root>` | Full — see `components/popover.md` |
| Tooltip | Base UI `Tooltip` | `<Tooltip.Root>` | Full — see `components/tooltip.md` |
| Toast | Base UI `Toast` + hand-rolled undo | `<Toast.Root>` + custom `useUndo` hook | PARTIAL — undo logic is ours; see `components/toast-undo.md` |
| Checkbox | Base UI `Checkbox` | Full including `Checkbox.Group` | Full — see `components/checkbox.md` |
| DatePicker | **react-aria `useDatePicker`** | Headless; own rendering | NONE in Base UI — see `components/datepicker.md` |
| CommandPalette | **cmdk** | `<Command.Root>` inside Base UI Dialog | NONE in Base UI — see `components/command-palette.md` |
| ListRow | Hand-rolled | `<div>` with keyboard nav + selection | NONE — see pattern in `components/list-row.md`. If virtualization is needed, drop `react-virtuoso` under the hand-rolled semantics; do not let it dictate the keyboard model. |
| Filter | Composed: Combobox + hand-rolled chip remove | — | NONE — see `components/filter.md` |
| StatusPill | Hand-rolled span + CSS conventions | — | NONE — see `components/status-pill.md` |
| EmptyState | Hand-rolled layout | — | NONE — see `components/empty-state.md` |
| Drawer | Base UI `Drawer` | `<Drawer.Root>` (+ `Provider`/`Portal`/`Backdrop`/`Viewport`/`Popup`/`SwipeArea`) | Full — modal by default; pass `modal={false}` + `disablePointerDismissal` for non-modal nav drawers. See `components/drawer.md`. |
| Tabs | Base UI `Tabs` | `<Tabs.Root>` + `<Tabs.List>` + `<Tabs.Tab>` + `<Tabs.Panel>` | Full — owns roving tabindex, arrow-key nav, Home/End, ARIA. See `components/tabs.md`. |
| Peek | Hand-rolled | `<aside role="complementary">` with Esc-at-window + `data-id` return-focus handoff | NONE — composition mirrors ListRow: the list row that opens the peek carries `data-id`, peek focuses it on close. See `components/peek.md`. Do not substitute Base UI `Drawer` — peek's non-modal inspection contract diverges from Drawer's navigation semantics. |

## Installation

```bash
pnpm add @base-ui/react cmdk react-aria
pnpm add -D @tailwindcss/postcss tailwindcss
```

Then create `postcss.config.mjs` in the project root:

```javascript
export default {
  plugins: {
    '@tailwindcss/postcss': {}
  }
};
```

Without the PostCSS plugin + config file, `@import 'tailwindcss'` in your globals.css compiles to zero utilities and all Tailwind classes silently no-op. The `tailwindcss` npm package alone is not enough in v4 — the v3 auto-detection behavior is gone.

Expected versions (verified by Task 1 spike, Apr 2026):

- `@base-ui/react` ^1.4.0 (stable; v1.0 shipped 2025-12-11)
- `cmdk` ^1.1.1
- `react-aria` ^3.48.0 (monorepo re-exports `@react-aria/*` — `useDatePicker` lives here)
- `tailwindcss` v4 (uses the new `@tailwindcss/postcss` plugin; no `tailwind.config.js` required — configure via `@theme` in CSS)
- Node ≥ 20, pnpm ≥ 9

Base UI's unstyled primitives have no Tailwind coupling — we style them via data-state attributes (`data-open`, `data-disabled`, etc.) and Tailwind v4 variants. react-aria's hooks return prop spreads; we attach them to our own elements. cmdk ships minimal default styles that we override. All three play well together; no shim or adapter layer is needed.

Peer deps worth noting: `react` ≥ 19 (Next 15 default), `react-dom` ≥ 19. Base UI 1.x dropped React 18 support mid-2025; if you are on React 18 you cannot upgrade Base UI past 0.7.x and this skill's guidance does not apply.

## Why not shadcn/ui

shadcn/ui is Radix-based and ships with opinionated Tailwind-styled components. It is excellent for getting to a v1 fast. We rejected it for Pine for two reasons:

1. **Stack alignment with Operate.** Operate uses Base UI, not Radix. Choosing shadcn would mean re-deriving every styling decision Operate already made on Base UI primitives — a waste of the reference architecture we explicitly picked. See `_decisions-ledger.md § 2026-04-17 item 1`.
2. **Unstyled-first matches our voice.** Linear/Superhuman/Rauno visual language is specific and tight; shadcn defaults would fight the tokens in `visual-design/references/tokens/`. Base UI is unstyled by design — every visual decision is ours, nothing to override. The cost (more upfront styling work) is paid back in the next 100 components because the tokens compose cleanly.

If we were starting a product without the Operate reference or the custom voice, shadcn would be a reasonable default. We are not. Do not silently swap it in.
