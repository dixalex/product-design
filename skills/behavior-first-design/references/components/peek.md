# Peek

A non-modal, right-side inspection surface that slides in to preview the focused entity without leaving the list. Distinct from Dialog (modal — focus trap, body scroll lock) and Drawer (typically left/bottom navigation). The peek is opened from a list row (Space), closes on Esc from anywhere, and returns focus to the originating row so the user never loses their place in the queue.

**No primitive in Base UI — hand-rolled.** Peek was not in the v0 Common 15, but was promoted to v0 after `examples/mini-lead-crm/components/lead-detail-peek.tsx` surfaced the gap; see `_decisions-ledger.md § 2026-04-21`. The Base UI gap list (`_decisions-ledger.md § 2026-04-17` item 1) already named six hand-rolled components — Peek is the seventh, added retroactively.

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: yes
validation_timing: n-a
modal: no
focus_trap: no
scroll_lock_on_body: no
close_on_outside_click: yes
close_on_tab: no
return_focus_on_close: yes
return_focus_target: originator-via-data-id
anchor_side: right
enter_motion_ms: 200
enter_easing: ease-out
role: complementary
aria_label_required: yes
required_testid: peek-panel
```

## Keyboard contract

Peek has no canonical spec (WAI-ARIA has no "peek" pattern); the contract below is Pine's house convention, cross-referenced with Linear's issue preview and Superhuman's conversation peek. Esc binding follows `keyboard-and-focus.md § On non-modal overlay dismissal`.

| Key | When | Effect |
| --- | --- | --- |
| `Space` | List row focused | Open peek for focused row; focus stays on row |
| `Space` | Peek open, focus on originating row | Close peek; row keeps focus |
| `Space` | Peek open, focus inside panel | Close peek; focus returns to originating row |
| `Esc` | Peek open, focus anywhere | Close peek; focus returns to originating row |
| `Tab` | Peek open, focus on row | Advance focus into peek panel contents |
| `Tab` | Peek open, focus at last focusable inside panel | Advance focus out of panel per DOM order (peek does NOT trap) |
| (pointer) click outside | Peek open | Close peek; focus returns to originator if it still exists |

Space as a close key is acceptable when the panel has no scrollable interactive children; if the peek grows to host a scrollable form or long transcript, restrict Space-close to the originating row only (panel Space should scroll per browser default). Flag noted in `_decisions-ledger.md § 2026-04-21`.

## Behavior invariants

- **Non-modal.** No focus trap; no body scroll lock. The list behind stays scrollable, clickable, and keyboard-reachable. This is the defining distinction from `components/dialog.md`.
- **Esc from anywhere closes the peek.** Implemented as a `window`-scoped `keydown` listener with a guard for open Base UI dialogs/popups — see `keyboard-and-focus.md § On non-modal overlay dismissal` for the exact pattern and rationale.
- **Focus returns to originator via `data-id` lookup.** On close, `requestAnimationFrame` runs a `document.querySelector` for `[data-id="<originatorId>"]` and focuses it. If the originator was deleted while peek was open, fall back to next-focusable row (`keyboard-and-focus.md § On content removed`) — never `<body>`.
- **200ms ease-out slide from the right.** Peek is a boundary-marker animation per `motion.md § Duration defaults` and `motion.md § The frequency-and-novelty matrix` — low-frequency-per-novelty-boundary (not a row toggle); earns the 200ms. Reduced motion collapses to instant opacity swap.
- **No dismissal ambiguity with selection.** Opening a peek does NOT change row selection state (`patterns/multi-select.md`) — peek is orthogonal to the selection axis, same as focus.
- **`role="complementary"` with `aria-label`.** The label includes the entity name (`aria-label="Detail for {lead.name}"`) so screen readers announce context on open.
- **Required `data-testid="peek-panel"`.** Pine's integration-test contract — Playwright specs assert visibility by this attribute.

## State transitions

```
closed
  ↓ Space on list row | row click with peek-on-click variant
opening (200ms ease-out slide from right; panel mounts; `originatorIdRef` set from focused row)
  ↓ animation ends
open (non-modal; focus stays on row unless user Tabs in; list behind remains active)
  ↓ Tab → focus advances into panel contents
  ↓ Esc | Space (row or panel) | outside-click | close-button
closing (opacity out; no exit translate required — the state change is what matters)
  ↓ animation ends
closed (focus → originator via data-id lookup; if originator deleted, focus → next-focusable)

  — while open —
  ↓ list row deleted (originator removed from DOM)
    → on close: focus falls to next-focusable row, never <body>
  ↓ higher-priority dialog opens on top (Base UI Dialog)
    → Esc routes to dialog first; peek's window listener bails via
      `[role="dialog"][data-state="open"]` guard
```

Reduced motion (`prefers-reduced-motion: reduce`) collapses the 200ms enter to instant (`motion.md § prefers-reduced-motion`); the opacity swap carries the state-change signal.

## Stack binding (Base UI)

No Base UI primitive. Hand-roll as `<aside role="complementary">` with the keyboard + focus-return contract above. Store the panel's open state in app state (e.g. `useStore((s) => s.peekOpenId)` — one peek at a time) so Esc handlers can read it globally. Use `data-id` on list rows and a ref captured on peek-open to bridge the return-focus handoff. Do NOT adopt a Drawer primitive for peek semantics — Base UI's `Drawer` is modal-leaning and meant for navigation; see `components/drawer.md` for the distinction. See `stack-bindings/web-primitives.md § Per-component binding` for the row in the binding table.

```tsx
// Consumer composition (sketch):
// import { Peek } from '@/components/peek';
//
// <Peek
//   openId={peekOpenId}
//   onClose={closePeek}
//   ariaLabel={`Detail for ${lead.name}`}
// >
//   <PeekHeader title={lead.name} onClose={closePeek} />
//   <PeekBody>{/* entity fields */}</PeekBody>
// </Peek>
//
// The list row that triggers it carries data-id="<entityId>"; Peek reads that id
// on open (via the useStore selector) and focuses [data-id="<id>"] on close.
```

## Common mistakes

- Building peek as a Dialog with `modal={false}` — inherits Dialog's focus trap expectations and breaks the non-modal contract (`components/dialog.md`; `anti-patterns.md § Behavior anti-patterns`).
- Esc handler only bound to the panel element — fails when focus is still on the originating row (the common case). Must be `window`-scoped with the dialog-open guard (`keyboard-and-focus.md § On non-modal overlay dismissal`).
- Returning focus to `<body>` when the originator was deleted — destroys the user's place in the list (`keyboard-and-focus.md § On content removed`).
- Animating the exit with a 200ms translate — the open gesture earns motion (novelty); the close does not, and a translate-out adds perceived latency when the user is dismissing to return to work.
- Locking body scroll — peek is non-modal; the user must be able to scroll the list behind while previewing (`motion.md § Conflict with HIG` context: we are a dense work tool, not an iOS sheet).
- Missing `aria-label` — screen readers announce "complementary region" with no context on what the peek is showing.
- Space closes peek when focus is inside a scrollable text area or form — conflicts with browser default (page-scroll / form-type); restrict Space-close to the originating row once panel gains scrollable content.

## Principles invoked

- Principle 1 (speed of interaction) — open paint ≤100ms; 200ms enter is the boundary-marker cap, not a stall.
- Principle 2 (keyboard-first) — Space opens, Esc closes, Tab traverses contents without trap.
- Principle 3 (focus is a managed cursor) — return focus to originator on close; never drop to `<body>`.
- Principle 5 (motion is semantic) — boundary-marker slide, never decorative; reduced-motion unconditional.
- Principle 6 (affordances on hover) — n/a at panel level, but the list row's Space affordance is per `components/list-row.md § Behavior invariants`.

(Principle numbers per SKILL.md.)

## Sources

- `components/lead-detail-peek.tsx` in `examples/mini-lead-crm/` — reference implementation shape (not contract).
- `keyboard-and-focus.md § On non-modal overlay dismissal` — Esc-on-window pattern with dialog-open guard.
- `keyboard-and-focus.md § On content removed` — originator-deleted fallback.
- `motion.md § Duration defaults` — 200ms peek/drawer enter.
- `motion.md § The frequency-and-novelty matrix` — boundary-marker exception.
- `voices/rauno-mechanics.md` — animate spatial relationships (peek slides from the right because it came from the direction of detail).
- `voices/linear.md` — detail content dominates; supporting chrome dims.
- `_decisions-ledger.md § 2026-04-21` — promotion from v1.1 to v0; Space-as-close v1.1 flag.
- `_decisions-ledger.md § 2026-04-17` item 1 — Base UI gap list (Peek added retroactively).
- `components/dialog.md` (modal contrast), `components/drawer.md` (navigation contrast), `components/list-row.md § Behavior invariants` (Space-opens-peek binding).
- `stack-bindings/web-primitives.md § Per-component binding` — hand-rolled row.
