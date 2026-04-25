# Tooltip

A short, non-interactive text label that appears on hover or focus to clarify an icon, truncated label, or keyboard shortcut. Content budget: ≤1 short sentence. Never the sole carrier of critical information — tooltips are lossy on touch and variable on assistive tech.

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: yes
keyboard_activation: focus
esc_closes: yes
validation_timing: n-a
opens_on_hover: yes
opens_on_focus: yes
interactive_content: no
default_open_delay_ms: 500
cascade_re_open_delay_ms: 0
cascade_window_ms: 1500
enter_motion_ms: 150
```

## Keyboard contract

Reproduced from Radix Primitives Tooltip (https://www.radix-ui.com/primitives/docs/components/tooltip#keyboard-interactions) and Base UI Tooltip (https://base-ui.com/react/components/tooltip); cross-referenced in `keyboard-and-focus.md § Anti-patterns (from Nordhealth)` (re: the `title`-attribute prohibition).

| Key | When | Effect |
| --- | --- | --- |
| `Tab` (focus in) | Trigger receives focus | Open tooltip after default delay (500ms first time; 0ms if within cascade window) |
| `Tab` (focus out) | Trigger loses focus | Close tooltip immediately |
| `Esc` | Tooltip open | Close tooltip; focus stays on trigger |
| (pointer) hover-in | Pointer enters trigger | Open tooltip after default delay |
| (pointer) hover-out | Pointer leaves trigger | Close tooltip after short grace period |

Tooltips are **keyboard-accessible by focus**, never keyboard-only via hover simulation. If the trigger cannot receive focus, it cannot carry a tooltip — fix the trigger, not the tooltip.

## Behavior invariants

- **500ms default open delay; 0ms on re-hover within 1.5s** — the tooltip cascade: once the user sees one tooltip in a group, subsequent tooltips open instantly. After 1.5s idle, delay resets to 500ms. Base UI / Radix expose this as `delayDuration` + `skipDelayDuration`.
- **Keyboard-accessible via focus, not just hover** — hover-only tooltips are invisible to keyboard and AT users (`keyboard-and-focus.md § Anti-patterns (from Nordhealth)`).
- **Esc dismisses** the open tooltip without moving focus.
- **Never for critical information** — tooltips are lossy (touch, AT, reflow). Critical info goes in the visible label or inline helper text (`patterns/error-surface.md`).
- **Content budget ≤1 short sentence** — longer or interactive content belongs in a Popover.
- **Enter 150ms** (`motion.md § Duration defaults`); reduced-motion swaps opacity instantly.

## State transitions

```
closed
  ↓ hover-in | focus-in
delay-pending (500ms; 0ms if cascade window active)
  ↓ timer elapsed
open (non-interactive; Floating UI positioned)
  ↓ hover-out (grace) | focus-out | Esc
closing → closed (cascade window opens for 1.5s)

cascade-window-active (1.5s after any tooltip closes)
  ↓ new trigger hover/focus → open with 0ms delay
  ↓ 1.5s elapsed → delay resets to 500ms
```

The cascade is a per-group behavior in Radix/Base UI via a `Tooltip.Provider` wrapper — wrap toolbars and icon clusters in one provider.

## Stack binding (Base UI)

Use `<Tooltip.Provider delay={500} closeDelay={0}>` wrapping the app or toolbar, then `<Tooltip.Root>` + `<Tooltip.Trigger>` + `<Tooltip.Positioner>` + `<Tooltip.Popup>` from `@base-ui/react`. The provider's `delay` / `closeDelay` pair implements the cascade. The primitive owns Floating UI positioning, hover/focus coordination, and Esc-to-close. Do NOT use the HTML `title` attribute as a substitute.

## Common mistakes

- `title` attribute as tooltip — no keyboard, no touch, no ARIA surface (`anti-patterns.md § Behavior anti-patterns`; `keyboard-and-focus.md § Anti-patterns (from Nordhealth)`).
- Tooltip as the only accessible name — use visually-hidden text or `aria-label` first; tooltip supplements only.
- Interactive content (link/button/input) inside a tooltip — use a Popover (`components/popover.md`).
- Tooltip carrying critical info (errors, required-field reasons) — escalate to inline text (`patterns/error-surface.md`).
- Global 0ms delay — every hover flashes a tooltip; the cascade exists to avoid this.
- Tooltip wider than the viewport — missing Floating UI `shift` middleware.

## Principles invoked

- Principle 2 (obvious affordances) — tooltips supplement, never replace, the primary affordance (visible label, icon, accessible name).
- Principle 3 (keyboard parity) — focus opens the tooltip; Esc closes it.
- Principle 4 (information scent / progressive disclosure) — ≤1 sentence by contract; longer content is a different component.
- Principle 6 (reversibility / safe defaults) — never the only carrier of critical info; fallback path must exist without the tooltip.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Radix Primitives, Tooltip — https://www.radix-ui.com/primitives/docs/components/tooltip
- Base UI, Tooltip — https://base-ui.com/react/components/tooltip
- Apple HIG, Tooltips (keyboard shortcuts + icon labels) — https://developer.apple.com/design/human-interface-guidelines/tooltips
- `keyboard-and-focus.md § Anti-patterns (from Nordhealth)` (the title-attribute rule)
- `anti-patterns.md § Behavior anti-patterns` (title as tooltip, aria-label where VH text works)
- `motion.md § Duration defaults`
- `patterns/error-surface.md` (critical info belongs inline, not in a tooltip)
- `components/popover.md` (when content needs to be interactive)
