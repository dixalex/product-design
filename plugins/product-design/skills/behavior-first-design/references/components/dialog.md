# Dialog

A modal overlay that interrupts the user's flow for a bounded task (confirm, edit a record, choose from a required set). Traps focus, locks body scroll, closes on Esc. Reserved for tasks that *cannot* proceed without user input — everything else should be inline-edit or a toast-with-undo. Non-modal variants (e.g., Base UI `modal={false}`) are out of scope here — use a popover or peek surface instead. See `components/popover.md` (Task 14+).

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: yes
validation_timing: on-submit
focus_trap: yes
first_focusable_on_open: yes
return_focus_on_close: yes
return_focus_fallback_when_trigger_deleted: next-focusable
scroll_lock_on_body: yes
```

## Keyboard contract

Reproduced from Radix Primitives (https://www.radix-ui.com/primitives/docs/components/dialog#keyboard-interactions); cross-referenced in `keyboard-and-focus.md § Dialog`.

| Key | When | Effect |
| --- | --- | --- |
| `Space` / `Enter` | Trigger focused | Open dialog |
| `Tab` | Dialog open | Move focus to next focusable element inside dialog; wraps to first after last |
| `Shift` + `Tab` | Dialog open | Move focus to previous focusable inside dialog; wraps to last from first |
| `Enter` | Default / primary button focused | Submit form or invoke primary action |
| `Esc` | Dialog open | Close dialog; return focus to trigger (or next-focusable if trigger deleted) |

Focus is **trapped**: Tab past the last element cycles to the first; Shift+Tab past the first cycles to the last. Focus never escapes to the page behind the overlay. This is the single biggest distinction from Dropdown Menu (which lets Tab close and advance).

## Behavior invariants

- **First focusable on open** — Atlassian's default is the close button in the header; fall back to title (with `tabIndex={-1}`) or dialog container (`keyboard-and-focus.md § On dialog open / close`).
- **Return focus to trigger on close**; if trigger was deleted, fall back to next-focusable, never `<body>` (`keyboard-and-focus.md § On dialog open / close`).
- **Body scroll is locked** and content behind is inert to scroll, click, and Tab — the behavioral contract of "modal."
- **Esc closes unconditionally** by default (`anti-patterns.md § Behavior anti-patterns`). Suppress only when suppression is load-bearing, and surface an explicit Cancel button instead.
- **Open paint ≤100ms, animation ≤150ms** (`response-time-budget.md § Numeric targets`, `motion.md § Duration defaults`); mid-transition click cancels animation.
- **Reserve for hard blocks** — single-field edits use inline-edit; reversible ops use toast-with-undo (`anti-patterns.md § Behavior anti-patterns`).

## State transitions

```
closed
  ↓ trigger click | Enter | Space
opening (animation up to 150ms; focus moved to first focusable)
  ↓ animation ends
open (focus trapped; body scroll locked)
  ↓ Tab / Shift+Tab cycles focus within trap
  ↓ submit primary action → committing → closing → closed (focus → trigger)
  ↓ Esc | close-button | overlay-click → closing → closed (focus → trigger or next-focusable)
  ↓ trigger deleted while open → on close: focus → next-focusable (not <body>)

committing (async action in flight; aria-busy=true; disable primary button)
  ↓ success → closing
  ↓ error → error-state (inline via patterns/error-surface.md; stay open)
```

Reduced motion (`prefers-reduced-motion: reduce`) collapses the 150ms transition to an instant state change — the dialog appears/disappears without animation (`motion.md § Easing` / reduced-motion rules).

## Stack binding (Base UI)

Use `<Dialog.Root>` + `<Dialog.Trigger>` + `<Dialog.Backdrop>` + `<Dialog.Popup>` + `<Dialog.Title>` + `<Dialog.Description>` from `@base-ui/react`. The primitive owns focus trap, scroll lock, Esc-to-close, and return-focus; you supply the body. Pass `finalFocus={yourRef}` for the trigger-deletion fallback. Always render `<Dialog.Title>` — wrap in `<VisuallyHidden>` if there's no visible title. See `stack-bindings/web-primitives.md` for fallbacks (Task 17 — forward reference).

## Common mistakes

- Dialog for a single-field edit — use `components/input-inline-edit.md` (`anti-patterns.md § Behavior anti-patterns`).
- Confirm dialog for a reversible op — use toast-with-undo (`patterns/error-surface.md`, `anti-patterns.md § Behavior anti-patterns`).
- Tab escapes the trap to the page behind — defining bug of a non-modal "modal."
- Focus returns to `<body>` after close — any trigger-deleted case must fall to next-focusable.
- Animation >300ms or spring entrance (`anti-patterns.md § Behavior anti-patterns`); 150ms ease-out is the default.
- No `<Dialog.Title>` (visible or visually-hidden) — the dialog is unnamed to AT.

## Principles invoked

- Principle 1 (speed of interaction) — 100ms open paint, 150ms transition cap.
- Principle 2 (obvious affordances) — close button focused by default; accessible title.
- Principle 3 (keyboard parity) — Esc closes, Tab trap cycles, Enter submits primary.
- Principle 6 (reversibility / safe defaults) — reserved for hard blocks only.
- Principle 7 (respect the user's cursor) — return to trigger or next-focusable, never `<body>`.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Radix Primitives, Dialog keyboard interactions — https://www.radix-ui.com/primitives/docs/components/dialog#keyboard-interactions
- Atlassian Design System, Modal Dialog focus management — https://atlassian.design/components/modal-dialog/examples#focus-management
- Base UI, Dialog — https://base-ui.com/react/components/dialog
- `keyboard-and-focus.md § Dialog`, `§ On dialog open / close`, `§ On content removed`
- `response-time-budget.md § Numeric targets`, `§ Perceived-performance techniques`
- `motion.md § Duration defaults`
- `patterns/error-surface.md`
- `anti-patterns.md § Behavior anti-patterns`
