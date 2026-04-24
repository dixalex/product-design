# Toast (with Undo)

A transient, non-blocking notification that confirms a mutation and — for reversible operations — exposes an Undo action for a bounded window. The canonical replacement for the confirm-modal anti-pattern. Optimistic apply first, then toast; auto-dismiss after 5–10s; stack limit typically 3.

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: no
keyboard_activation: both
esc_closes: yes
validation_timing: n-a
optimistic_apply_before_toast: yes
undo_available: yes
undo_window_seconds: 5
auto_dismiss_ms: 5000
error_toast_persistent: yes
stack_limit: 3
stack_overflow_behavior: collapse-older
aria_live: polite
swipe_to_dismiss: yes
pause_on_hover_or_focus: yes
enter_motion_ms: 150
```

## Keyboard contract

Reproduced from Sonner (emilkowalski/sonner — https://sonner.emilkowalski.com/) and Radix Primitives Toast (https://www.radix-ui.com/primitives/docs/components/toast#keyboard-interactions).

| Key | When | Effect |
| --- | --- | --- |
| `F6` | Any focus | Move focus into the toast viewport (Radix convention; surfaces toasts for keyboard users) |
| `Tab` | Toast focused | Advance focus to Undo / action button inside the toast |
| `Enter` | Undo button focused | Invoke undo; dismiss toast; return focus to prior location |
| `Space` | Undo button focused | Invoke undo; dismiss toast |
| `Esc` | Toast focused | Dismiss toast without invoking the action |
| `Shift` + `Tab` | Undo focused | Retreat focus out of the toast viewport |

Auto-dismiss **pauses** while the toast has focus or pointer hover — keyboard users who Tab into a toast get as much time as they need to read and decide.

## Behavior invariants

- **Optimistic apply before the toast** — mutation lands locally first; toast confirms. Never "are you sure?" before a reversible action (`anti-patterns.md § Behavior anti-patterns` — confirm modal for reversible operations; `components/dialog.md` contrast).
- **Undo available for the full auto-dismiss window** (5–10s default; 7s is a good CRM middle-ground). After dismiss, undo is gone — the window is the contract.
- **Errors persist** until user-acknowledged; success/info toasts auto-dismiss (`motion.md § Duration defaults`; `patterns/error-surface.md`).
- **Stack limit 3** — older toasts collapse into "+N more" or compress (Sonner default).
- **`aria-live="polite"`** by default; `assertive` only for critical errors.
- **Swipe-to-dismiss on touch; Esc on keyboard** — every toast has a manual dismissal path.
- **Enter 150ms; auto-dismiss 5s** (`motion.md § Duration defaults`). Reduced-motion swaps opacity instantly; the dismiss timer keeps running.
- **Pause on hover or focus** — keyboard users who Tab in get as long as they need.

## State transitions

```
(mutation committed optimistically) → fire toast
entering (150ms fade + slight translate-up)
  ↓ animation ends
visible (auto-dismiss timer running; 5–10s)
  ↓ hover-in | focus-in → paused (timer frozen)
  ↓ hover-out | focus-out → visible (timer resumes)
  ↓ Undo → undo-in-flight → reversed → dismissing → closed
  ↓ Esc | swipe | close-button → dismissing → closed
  ↓ timer elapses → auto-dismissing → closed (mutation final)

stack-overflow (≥4 visible) → oldest collapses into "+N more" or compresses

error-variant → no auto-dismiss; stays until close or action
```

If Undo itself fails (server rejects the reversal), surface an error toast or inline error (`patterns/error-surface.md`) — never silently leave the mutation applied.

## Stack binding (Base UI)

Base UI does not ship a Toast primitive (one of the Base UI gaps per `_decisions-ledger.md § 2026-04-17`). Use `sonner` (emilkowalski) as primary — matches the full contract out of the box (stacking, action button, swipe, pause-on-hover, F6, `aria-live`). Fall back to Radix Primitives `Toast` if the project already depends on Radix. Configure `duration={5000}`, Sonner `expand` for stacking, custom `action` for Undo. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Confirm modal before a reversible action (use optimistic + toast-with-undo) — `anti-patterns.md § Behavior anti-patterns`; `components/dialog.md` is NOT for this.
- No optimistic apply — user waits on server, then sees a toast. Spinner-shaped delay is the bug (`response-time-budget.md § Perceived-performance techniques` #1).
- Auto-dismissing error toasts — errors persist until acknowledged (`patterns/error-surface.md`).
- Undo button as a `<div onClick>` — fails Space activation and AT (`anti-patterns.md § Accessibility anti-patterns`; `components/button.md`).
- Stack of 10+ toasts covering the viewport — enforce stack limit and collapse.
- Silent undo failure — always surface the reversal's result (`patterns/error-surface.md`).
- Toast as the only record of a destructive outcome — pair with an activity log entry so missed toasts are recoverable.

## Principles invoked

- Principle 1 (speed of interaction) — optimistic apply <50ms; toast enters within 150ms.
- Principle 3 (keyboard parity) — F6 focuses the viewport; Enter invokes Undo; Esc dismisses.
- Principle 5 (peak-end craft) — `foundations.md § Peak-End Rule`: the confirmation is peak-end territory. Reinforced by `voices/eagle.md § Actionable principles` (principle 5 — four feedback channels).
- Principle 6 (reversibility / safe defaults) — Undo is the reversibility mechanism. Reinforced by `voices/eagle.md § Actionable principles` (principle 8 — every action undoable).
- Principle 7 (respect the user's cursor) — no focus steal; pause on hover/focus; return to prior location after Undo.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Sonner (emilkowalski) — https://sonner.emilkowalski.com/
- Radix Primitives, Toast — https://www.radix-ui.com/primitives/docs/components/toast
- Polaris (Shopify) undo doctrine — https://polaris-react.shopify.com/components/feedback-indicators/toast
- `foundations.md § Peak-End Rule`
- `motion.md § Duration defaults` (Toast enter 150ms; auto-dismiss 5s; errors persistent)
- `patterns/error-surface.md` (transient vs blocking errors)
- `voices/eagle.md § Actionable principles` (principle 5 — four feedback channels; principle 8 — undo everywhere)
- `components/dialog.md` (contrast — confirm modal is the anti-pattern)
- `anti-patterns.md § Behavior anti-patterns` (confirm modal for reversible operations)
- `response-time-budget.md § Perceived-performance techniques` (#1 Optimistic UI)
