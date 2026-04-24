# Checkbox

A binary (or tri-state) toggle input that expresses a boolean selection — single-use for opt-ins and settings, grouped for multi-select lists and bulk-select rows. Supports an indeterminate third state for partial-child selection (the "some but not all" case common in list-row bulk operations).

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: Space
esc_closes: n-a
validation_timing: on-submit
indeterminate_state: yes
aria_checked_values: "true | false | mixed"
group_semantics: fieldset-legend
label_click_toggles: yes
min_hit_target_px: 40
```

## Keyboard contract

Reproduced from react-aria `useCheckbox` / `useCheckboxGroup` (https://react-spectrum.adobe.com/react-aria/useCheckbox.html, https://react-spectrum.adobe.com/react-aria/useCheckboxGroup.html) and Base UI Checkbox (https://base-ui.com/react/components/checkbox). Space toggles; Enter does NOT — Enter is reserved for form submission.

| Key | When | Effect |
| --- | --- | --- |
| `Space` | Checkbox focused | Toggle checked state (true ↔ false; mixed → true) |
| `Tab` | Checkbox focused | Advance focus to next focusable element |
| `Shift` + `Tab` | Checkbox focused | Move focus to previous focusable |
| `Enter` | Checkbox focused inside a form | Submit the form (NOT toggle — this is the canonical distinction from Button) |
| (pointer) click on checkbox | Any time | Toggle checked state |
| (pointer) click on associated label | Any time | Toggle checked state (label is a Fitts-expanded hit target) |

The Space-toggle / Enter-submit split is the single most common regression when replacing a native `<input type="checkbox">` with a `<div>` — always normalize via Base UI / react-aria rather than hand-wiring keyboard handlers.

## Behavior invariants

- **Three ARIA states: `aria-checked="true" | "false" | "mixed"`** — mixed represents a parent whose children are partially selected (canonical list-header select-all).
- **Indeterminate toggles to checked on activation** — Space/click on mixed moves to `true`, not `false` (react-aria and Base UI enforce this).
- **Label is clickable; expands hit target** — wrap checkbox + label in one `<label>` (or use `htmlFor`). Turns ~16px into a full-row target per `foundations.md § Fitts's Law`.
- **Minimum hit target ≥40×40 CSS pixels** including label; icon-only checkboxes in dense tables are the most common violation.
- **Groups use `<fieldset>` + `<legend>`**; single-use checkboxes stand alone with a label.
- **Disabled not focusable by default**; use `aria-disabled` only when the Tab stop is load-bearing (same rule as Button).
- **No motion on toggle** — high-freq/low-novelty (`motion.md § The frequency-and-novelty matrix (Rauno)`); instant swap.

## State transitions

```
unchecked (aria-checked="false")
  ↓ Space | click on box | click on label
checked (aria-checked="true")
  ↓ Space | click
unchecked

mixed / indeterminate (aria-checked="mixed"; parent of partially-selected group)
  ↓ Space | click
checked (all children become selected)
  ↓ external change (child deselected)
mixed

any → disabled (prop change; removes from tab order unless aria-disabled)
disabled → any (prop change back)
```

No motion on any transition (`motion.md § Duration defaults` — 0ms for row select, button press, keyboard nav).

## Stack binding (Base UI)

Use `<Checkbox.Root>` + `<Checkbox.Indicator>` from `@base-ui/react`; wrap with a `<label>` so label clicks toggle. For indeterminate, pass `indeterminate={true}` (the primitive sets `aria-checked="mixed"` and handles mixed→true on activation). For groups, wrap in `<CheckboxGroup.Root>` + `<fieldset>` + `<legend>`. Do NOT hand-roll `<div role="checkbox">`. See `stack-bindings/web-primitives.md` for react-aria fallback via `useCheckbox` / `useCheckboxGroup` (Task 17 — forward reference).

## Common mistakes

- `<div onclick>` instead of a real checkbox — loses Space activation and `aria-checked` (`anti-patterns.md § Accessibility anti-patterns`).
- Enter toggles the checkbox — breaks form-submit; Space is the correct key.
- Indeterminate visual without `aria-checked="mixed"` — AT sees nothing.
- Label not clickable — small hit target (`foundations.md § Fitts's Law`).
- Hit target <40×40 CSS pixels.
- Color-only selected state (no glyph / no `aria-checked`) — `anti-patterns.md § Behavior anti-patterns`.
- Motion on toggle (high-freq/low-novelty) — `anti-patterns.md § Behavior anti-patterns`.
- Checkbox semantics for single-choice (use radio) or commands (use button).

## Principles invoked

- Principle 2 (obvious affordances) — check glyph + `aria-checked`; disabled *looks* disabled; label expands the hit target.
- Principle 3 (keyboard parity) — Space toggles, Enter submits, Tab advances, focus-visible ring.
- Principle 4 (information scent / progressive disclosure) — indeterminate communicates partial-group state without a separate chip.
- Principle 6 (reversibility / safe defaults) — toggle is trivially reversible; destructive consequences belong on submit or toast-with-undo.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useCheckbox` — https://react-spectrum.adobe.com/react-aria/useCheckbox.html
- react-aria, `useCheckboxGroup` — https://react-spectrum.adobe.com/react-aria/useCheckboxGroup.html
- Base UI, Checkbox — https://base-ui.com/react/components/checkbox
- WAI-ARIA Checkbox pattern — https://www.w3.org/WAI/ARIA/apg/patterns/checkbox/
- `foundations.md § Fitts's Law` (label expands hit target, 40×40 minimum)
- `patterns/multi-select.md` (how bulk-select composes on row-level checkboxes)
- `motion.md § The frequency-and-novelty matrix (Rauno)`, `§ Duration defaults`
- `keyboard-and-focus.md § Keyboard contracts per archetype`
- `anti-patterns.md § Behavior anti-patterns`, `§ Accessibility anti-patterns`
