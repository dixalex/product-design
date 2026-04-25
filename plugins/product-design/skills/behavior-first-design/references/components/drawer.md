# Drawer

A side-anchored overlay panel, usually from the left or bottom, hosting primary or secondary navigation — not a record-inspection surface. Distinct from Peek (right-side, non-modal inspection of a focused entity; see `components/peek.md`) and Dialog (centered modal, blocking a bounded task; see `components/dialog.md`). Drawer's defining trait is that it is a *navigation* surface, which is why modal is the default but non-modal is supported when the drawer must coexist with active work behind it.

Base UI 1.x ships `@base-ui/react/drawer` — use it. Do not hand-roll.

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
modal: yes            # default; set false for non-modal nav drawer
focus_trap: yes       # follows `modal`
scroll_lock_on_body: yes   # follows `modal`
close_on_outside_click: yes
close_on_tab: no
return_focus_on_close: yes
anchor_side: left     # or bottom | right | top; left is the navigation default
enter_motion_ms: 200
enter_easing: ease-out
swipe_dismiss: yes    # mobile — Base UI ships SwipeArea
snap_points_supported: yes
title_required: yes
```

## Keyboard contract

Reproduced from Base UI Drawer docs (https://base-ui.com/react/components/drawer) and WAI-ARIA Dialog pattern (https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/); cross-referenced in `keyboard-and-focus.md § On dialog open / close` (modal variant) and `keyboard-and-focus.md § On non-modal overlay dismissal` (non-modal variant).

| Key | When | Effect |
| --- | --- | --- |
| `Space` / `Enter` | Trigger focused | Open drawer; focus → first focusable inside (or configured `initialFocus`) |
| `Tab` | Drawer open, modal | Cycle focus inside drawer; trapped at edges |
| `Tab` | Drawer open, non-modal | Advance focus out of drawer per DOM order |
| `Shift` + `Tab` | Drawer open, modal | Cycle backward; trapped at edges |
| `Esc` | Drawer open (modal or non-modal) | Close drawer; focus returns to trigger |
| (pointer) click outside | Drawer open, default | Close drawer; focus returns to trigger |
| (pointer) click outside | `disablePointerDismissal` | Stay open — for non-modal nav drawers |
| (touch) swipe | Drawer open, `<Drawer.SwipeArea>` attached | Dismiss in the dismissal direction; snap to snap point if configured |

The modal variant matches Dialog's keyboard contract one-for-one — not surprising since Base UI's Drawer and Dialog share the floating-UI / focus-scope machinery. The difference is spatial: Drawer anchors to a side; Dialog centers.

## Behavior invariants

- **Modal by default — non-modal is opt-in.** Pass `modal={false}` + `disablePointerDismissal={true}` on `<Drawer.Root>` for navigation drawers that must stay open alongside work (inbox-style "all folders" sidebar that a user can toggle). Per Base UI docs: "To disable focus trapping and allow clicks outside the drawer to keep it open, set the `modal` prop to `false` and `disablePointerDismissal` to `true`."
- **First focusable on open** — Base UI handles this automatically via its focus-scope; override with `initialFocus` on `<Drawer.Popup>` for container-focus or title-focus cases (`keyboard-and-focus.md § On dialog open / close`). For a nav drawer whose first link is the user's most common destination, container-focus is usually preferable to link-focus (pressing Enter by accident shouldn't navigate).
- **Return focus to trigger on close** — Base UI manages it; pass `finalFocus` only when the trigger may be deleted (rare for nav drawers — the trigger is in the chrome).
- **200ms ease-out from the anchored side** per `motion.md § Duration defaults` — same budget as peek because both are boundary markers with spatial translation. Exits use `ease-in` and can be faster (often 150ms) since the user has already committed to leaving.
- **Scroll lock on body follows `modal`** — locked when modal, free when non-modal. Never diverge from the `modal` prop's implied contract; users infer "is the world behind this still alive?" from scroll behavior.
- **Swipe area on touch.** Mobile drawers attach `<Drawer.SwipeArea>` along the anchored edge. Desktop-only drawers skip it. The skill's v0 is desktop-first (`_v2-gaps.md § Mobile / responsive`) — note swipe support, don't implement unless mobile is in scope.
- **Snap points are optional.** Base UI supports `snapPoints` and `defaultSnapPoint` for partially-open drawers (e.g., half-height bottom sheets). Do not use snap points on a desktop nav drawer — snap points are a mobile-sheet pattern and introduce a second interaction mode for little benefit on desktop.
- **Required `<Drawer.Title>`** — visible or `<VisuallyHidden>`. Unnamed drawer is unnamed to assistive tech, same rule as Dialog.

## State transitions

```
closed
  ↓ trigger click | Enter | Space
opening (200ms slide in from anchored side; focus moves to Popup or configured initialFocus)
  ↓ animation ends
open, modal (focus trapped; body scroll locked)
open, non-modal (no trap; body scrollable; user may Tab out per DOM order)
  ↓ Tab / Shift+Tab navigates content (modal: cycles; non-modal: exits)
  ↓ Esc | close button | outside click (if enabled) | swipe (if SwipeArea present)
closing (slide out; reverse direction; often ease-in, ~150ms)
  ↓ animation ends
closed (focus → trigger; or finalFocus fallback if trigger deleted)
  ↓ snap-point change (optional, bottom-sheet pattern) → resize to new snap position without closing

nested drawer case:
  outer drawer open → trigger opens inner drawer
  Esc on inner → closes inner first (focus returns to outer trigger)
  Esc again → closes outer
```

Reduced motion (`prefers-reduced-motion: reduce`) collapses the 200ms slide to instant (`motion.md § prefers-reduced-motion`); the opacity / visibility swap carries the state change.

## Stack binding (Base UI)

Use `<Drawer.Provider>` + `<Drawer.Root>` + `<Drawer.Trigger>` + `<Drawer.Portal>` + `<Drawer.Backdrop>` + `<Drawer.Viewport>` + `<Drawer.Popup>` + `<Drawer.Content>` + `<Drawer.Title>` + `<Drawer.Description>` + `<Drawer.Close>` from `@base-ui/react/drawer`. Attach `<Drawer.SwipeArea>` if touch dismissal is required. The primitive owns focus trap (when modal), scroll lock, Esc-to-close, outside-click-dismiss, and return-focus. You supply the side (via positioner or CSS anchored to viewport edge) and the body. For non-modal navigation usage, pass `modal={false}` and `disablePointerDismissal={true}` on `<Drawer.Root>`. See `stack-bindings/web-primitives.md § Per-component binding` for the library row.

## Common mistakes

- Drawer used for entity inspection — that's a peek (`components/peek.md`). Drawer is for navigation; peek is for "preview this row."
- Non-modal drawer without `disablePointerDismissal` — drawer closes on every stray click outside, defeating its purpose.
- Modal drawer with no `<Drawer.Title>` — screen readers announce an unnamed region.
- Snap points on a desktop nav drawer — unnecessary complexity; reserve for mobile bottom sheets.
- Animation >300ms or spring entrance — violates `motion.md § Easing`.
- Focus returns to `<body>` after trigger-delete flow — always provide `finalFocus` fallback for any delete-while-open case (`keyboard-and-focus.md § On content removed`).

## Principles invoked

- Principle 1 (speed of interaction) — 200ms enter cap; 100ms open-paint target unchanged.
- Principle 2 (keyboard-first) — Esc closes, Tab trap cycles (modal) or exits (non-modal), Enter/Space opens from trigger.
- Principle 3 (focus is a managed cursor) — return focus to trigger; `finalFocus` fallback when trigger deleted.
- Principle 5 (motion is semantic) — boundary-marker slide from anchored side; never decorative.

(Principle numbers per SKILL.md.)

## Sources

- Base UI Drawer — https://base-ui.com/react/components/drawer
- Base UI accessibility overview — https://base-ui.com/react/overview/accessibility
- WAI-ARIA Dialog (modal) pattern — https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/
- `components/peek.md` (right-side inspection contrast), `components/dialog.md` (centered modal contrast)
- `keyboard-and-focus.md § On dialog open / close`, `§ On non-modal overlay dismissal`, `§ On content removed`
- `motion.md § Duration defaults`, `§ The frequency-and-novelty matrix`, `§ prefers-reduced-motion`
- `stack-bindings/web-primitives.md § Per-component binding`
- `_decisions-ledger.md § 2026-04-21` (promotion from v1.1 deferred to v0)
- `_v2-gaps.md § Mobile / responsive behavior specs` (swipe dismissal)
