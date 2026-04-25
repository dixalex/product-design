# Popover

A non-modal overlay anchored to a trigger, hosting interactive content (quick form, mini filter, inline picker). Distinct from Dialog (non-modal, no focus trap by default, no scroll lock) and from Tooltip (interactive content allowed, longer lifespan, opened by click/focus rather than hover).

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: yes
validation_timing: on-blur
modal: no
focus_trap: no
scroll_lock_on_body: no
collision_detection: yes
close_on_outside_click: yes
close_on_tab: no
return_focus_on_close: yes
anchor_side: bottom
anchor_align: start
open_paint_ms: 100
enter_motion_ms: 150
```

## Keyboard contract

Reproduced from Radix Primitives Popover (https://www.radix-ui.com/primitives/docs/components/popover#keyboard-interactions) and Base UI Popover (https://base-ui.com/react/components/popover); cross-referenced in `keyboard-and-focus.md § Keyboard contracts per archetype`.

| Key | When | Effect |
| --- | --- | --- |
| `Enter` / `Space` | Trigger focused | Open popover; move focus to first focusable inside |
| `Tab` | Popover open | Advance focus through popover content; leaving the popover does NOT auto-close |
| `Shift` + `Tab` | Popover open, first focusable | Move focus backward; past the trigger, focus follows normal DOM order |
| `Esc` | Popover open | Close popover; return focus to trigger |
| (pointer) click outside | Popover open | Close popover; focus returns to trigger only if focus was inside |

Unlike Dropdown Menu, **Tab does not close the popover** — Popover is designed to host inline forms where Tab must traverse the form fields. Close on outside click and Esc only.

## Behavior invariants

- **Non-modal by default** — page behind stays scrollable, clickable, Tab-reachable. No focus trap unless content requires it (rare — escalate to Dialog).
- **Collision detection mandatory** — Floating UI `flip`, `shift`, `hide` middleware; never render off-screen.
- **Close on outside click AND Esc, NOT on Tab** — defining difference from Dropdown Menu. Tab traverses inline-form content. See `components/dropdown-menu.md`.
- **Return focus to trigger on close** when focus was inside (`keyboard-and-focus.md § On dialog open / close`). If the user Tab-escaped elsewhere, leave their cursor alone (`keyboard-and-focus.md § When NOT to move focus (Polaris doctrine — critical)`).
- **Open paint ≤100ms; enter 150ms** (`response-time-budget.md § Numeric targets`, `motion.md § Duration defaults`); reduced-motion swaps opacity instantly.
- **Accessible name** — icon-only trigger carries visually-hidden text; popover itself may use `aria-labelledby` referencing its heading.

## State transitions

```
closed
  ↓ trigger click | Enter | Space
opening (150ms; anchor resolved; collision middleware runs)
  ↓ animation ends
open (non-modal; focus → first focusable inside)
  ↓ Tab / Shift+Tab navigates content (does NOT close)
  ↓ submit inline form → committing → closing → closed (focus → trigger)
  ↓ Esc | outside-click → closing → closed (focus → trigger if inside, else leave cursor)
  ↓ viewport resize/scroll → re-resolve position

committing (aria-busy on primary button)
  ↓ success → closing
  ↓ error → error-state (inline via patterns/error-surface.md; stay open)
```

Reduced motion collapses the 150ms enter to instant (`motion.md § prefers-reduced-motion`).

## Stack binding (Base UI)

Use `<Popover.Root>` + `<Popover.Trigger>` + `<Popover.Positioner>` + `<Popover.Popup>` from `@base-ui/react`. The primitive owns Floating-UI collision detection, outside-click close, Esc-to-close, and return-focus. Configure `side` (`top` | `right` | `bottom` | `left`) and `align` (`start` | `center` | `end`). Do NOT use `modal={true}` — if you need a blocking workflow, use `components/dialog.md`. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Popover where Dialog is correct (blocking workflow, destructive confirm) — no focus trap means Tab escapes mid-task (`anti-patterns.md § Behavior anti-patterns`; `components/dialog.md`).
- Popover where Tooltip is correct (static descriptive text) — popover is click/focus-triggered (`components/tooltip.md`).
- No collision middleware — renders off-screen on small viewports.
- Trapping Tab inside a non-modal popover — breaks the "Tab escapes" contract.
- Closing on Tab (copying Dropdown Menu) — users can't fill the inline form.
- Animation >300ms or spring entrance (`anti-patterns.md § Behavior anti-patterns`; `motion.md § Easing`).
- Focus returns to `<body>` when trigger was removed — fall to next-focusable (`keyboard-and-focus.md § On content removed`).

## Principles invoked

- Principle 1 (speed of interaction) — 100ms open paint, 150ms enter cap.
- Principle 2 (obvious affordances) — accessible name; collision-detected anchor never clips.
- Principle 3 (keyboard parity) — Enter/Space opens, Esc closes, Tab navigates content.
- Principle 6 (reversibility / safe defaults) — non-modal by default; escalate to Dialog only for hard blocks.
- Principle 7 (respect the user's cursor) — return focus only when focus was inside; never steal on background updates.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Radix Primitives, Popover — https://www.radix-ui.com/primitives/docs/components/popover
- Base UI, Popover — https://base-ui.com/react/components/popover
- Floating UI middleware — https://floating-ui.com/docs/middleware
- `components/dialog.md` (modal contrast), `components/tooltip.md` (hover contrast), `components/dropdown-menu.md` (Tab-close contrast)
- `keyboard-and-focus.md § On dialog open / close`, `§ When NOT to move focus (Polaris doctrine — critical)`
- `response-time-budget.md § Numeric targets`
- `motion.md § Duration defaults`, `§ prefers-reduced-motion`
- `anti-patterns.md § Behavior anti-patterns`
