# Button

A press target that invokes an action on a single click, tap, or keyboard activation.

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: n-a
validation_timing: n-a
aria_busy_when_loading: yes
min_hit_target_px: 40
```

## Keyboard contract

Reproduced from react-aria `useButton` Features (https://react-spectrum.adobe.com/react-aria/useButton.html) and Base UI `Button` docs (https://base-ui.com/react/components/button).

| Key | When | Effect |
| --- | --- | --- |
| `Enter` | Button is focused and not disabled | Activate (fires `onPress` / `onClick`) |
| `Space` | Button is focused and not disabled (keydown) | Begin press; release on keyup activates |
| `Tab` | Button is focused | Advance focus to next focusable element |
| `Shift` + `Tab` | Button is focused | Move focus to previous focusable element |

Both Enter AND Space must activate. Missing Space activation is the single most common native-`<div>`-as-button regression — react-aria's press events normalize this across pointer, keyboard, and touch.

## Behavior invariants

- **Disabled buttons are not focusable by default** and do not receive press events; use the `aria-disabled` pattern (focusable, announced, press suppressed) only when you need to preserve the Tab stop to explain *why* it is disabled.
- **`:focus-visible`, never `:focus`** — pointer users never see the ring, keyboard users always do. Removing the outline without a replacement is a shipping-blocker (`keyboard-and-focus.md § Principles`).
- **Loading buttons set `aria-busy="true"`** and suppress activation until the pending action resolves. The visible label should not collapse; swap the content for a spinner of equal width to prevent layout shift.
- **Icon-only buttons have an accessible name** via visually-hidden text (preferred) or `aria-label` (fallback). `title=""` tooltips do not count — no keyboard, no mobile, no AT surface (`anti-patterns.md § Behavior anti-patterns`).
- **Minimum hit target ≥40×40 CSS pixels** per Fitts's Law (`foundations.md § Fitts's Law`); icon-only buttons pad the hit area beyond the visual bounds if needed.
- **Single click/tap only** — no double-click primary actions (`anti-patterns.md § Behavior anti-patterns`).

## State transitions

```
idle → hover (pointer only, no state change for keyboard)
idle → focus-visible (keyboard Tab-in)
idle | focus-visible → pressing (pointerdown / keydown Enter/Space)
pressing → idle | focus-visible (pointerup / keyup, if still over target)
pressing → cancelled (pointerleave before pointerup)
idle → loading (async onPress returns a Promise; aria-busy=true)
loading → idle (promise resolves; button re-enables)
any → disabled (prop change; removes from tab order unless aria-disabled)
```

Press feedback paints within 16ms (`response-time-budget.md § Numeric targets`, row 1). Loading spinner appears only if the pending action exceeds 200ms; shorter operations should stay optimistic with no transient UI (`response-time-budget.md § Perceived-performance techniques` #2).

## Stack binding (Base UI)

Use `<Button>` from `@base-ui/react`. Children are the visible label; pass `render` for polymorphism (e.g., `render={<a href="…" />}` to render as a link while keeping Button behavior). For icon-only variants, wrap the icon in a `<span className="sr-only">` with the accessible name, or pass `aria-label` if the label would be redundant with adjacent text. For async work, toggle `aria-busy` and a local `loading` prop yourself — Base UI's Button is a semantic primitive and does not own the loading state. See `stack-bindings/web-primitives.md` for fallbacks when Base UI is not available (Task 17 — forward reference).

## Common mistakes

- `<div onclick>` or `<span onclick>` without a keyboard handler — fails Space activation and is invisible to AT (`anti-patterns.md § Accessibility anti-patterns`).
- Removing the focus ring with `outline: none` and no replacement signifier — keyboard users navigate blind (`anti-patterns.md § Accessibility anti-patterns`).
- Using `title="…"` as the only accessible name on an icon-only button — tooltip is mouse-hover-only, no keyboard, no AT (`anti-patterns.md § Behavior anti-patterns`).
- Showing a spinner for operations under 200ms instead of staying optimistic — the flash reads as a bug (`anti-patterns.md § Behavior anti-patterns`).
- Animating press feedback longer than 150ms on frequent actions — motion on repeated interactions (`anti-patterns.md § Behavior anti-patterns`).
- More than one primary button per view — encodes multiple "next actions" and forces a second decision. See `foundations.md § Hick's Law`.

## Principles invoked

- Principle 1 (speed of interaction) — 16ms press paint, 200ms spinner threshold.
- Principle 2 (obvious affordances) — `:focus-visible` ring, minimum hit target, accessible name on every variant.
- Principle 3 (keyboard parity) — Enter AND Space activate; Tab order preserved.
- Principle 6 (reversibility / safe defaults) — disabled vs. `aria-disabled` decision is explicit; no destructive double-click.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useButton` — https://react-spectrum.adobe.com/react-aria/useButton.html
- Base UI, `Button` — https://base-ui.com/react/components/button
- `anti-patterns.md § Behavior anti-patterns`, `§ Accessibility anti-patterns`
- `foundations.md § Fitts's Law`, `§ Norman's Affordances + Signifiers`
- `keyboard-and-focus.md § Principles`, `§ Anti-patterns (from Nordhealth)`
- `response-time-budget.md § Numeric targets`, `§ Perceived-performance techniques`
