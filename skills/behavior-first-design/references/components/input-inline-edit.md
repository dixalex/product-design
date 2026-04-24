# Inline-edit input

A read-only field that reveals a text input in place when activated, commits on blur or Enter, cancels on Esc. The default for single-field edits in a CRM — avoids a dialog round-trip and preserves the user's spatial context.

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: n-a
hover_affordance_hidden_in_idle: yes
keyboard_activation: both
esc_closes: yes
validation_timing: on-blur
placeholder_on_empty: yes
autofocus_on_enter_edit: yes
commit_on_blur: yes
commit_on_enter: yes
cancel_on_esc: yes
aria_invalid_on_error: yes
```

## Keyboard contract

Distilled from react-aria `useTextField` (https://react-spectrum.adobe.com/react-aria/useTextField.html) and Notion / Linear observed inline-edit behavior.

| Key | When | Effect |
| --- | --- | --- |
| `Enter` / `Space` | Read-only cell is focused | Enter edit mode; input receives focus; full text selected |
| `F2` | Read-only cell is focused (grid contexts) | Enter edit mode (WAI-ARIA grid parity) |
| `Enter` | Editing | Commit value, exit edit mode, advance focus to next editable cell |
| `Tab` | Editing | Commit value, advance focus normally |
| `Shift` + `Tab` | Editing | Commit value, move focus to previous focusable |
| `Esc` | Editing | Revert to previous value, exit edit mode, focus returns to the cell |
| Click outside | Editing | Commit on blur (same as Tab) |

Enter commits *and* advances — the "save + next" shape is what makes inline edit feel fast in Linear and Notion. Don't require the user to Tab after Enter.

## Behavior invariants

- **Idle cell looks like text, not a field** — no border, no box. A hover affordance (subtle underline, edit glyph) appears on pointer hover only; keyboard focus shows the `:focus-visible` ring instead (`foundations.md § Norman's Affordances + Signifiers`).
- **Entering edit mode paints within 50ms** (`response-time-budget.md § Numeric targets`, "inline-edit open"). Anything slower reads as a dialog, not an inline affordance.
- **Full text is selected on entry** — users most often *replace* a value, not append to it. Matches native `<input>` double-click behavior.
- **Validation fires on-blur, not on-change** (`patterns/form-validation-timing.md § Decision table`). Mid-typing errors are accusatory; wait for the "I'm done" signal.
- **On validation error**: `aria-invalid="true"`, error message via `aria-describedby`, input stays in edit mode so the user can correct without losing context.
- **Esc reverts to the last committed value**, never to an intermediate buffer. The contract is "Esc = nothing happened."

## State transitions

```
idle (read-only)
  ↓ click | Enter | Space | F2
focused-edit (input visible, text selected, autofocus)
  ↓ typing
typing (value buffered, no validation yet)
  ↓ blur | Tab | Shift+Tab              → validating
  ↓ Enter                                → validating (+ advance on success)
  ↓ Esc                                   → idle (value reverted)

validating
  ↓ pass → committed → idle (optimistic; server reconciles in background)
  ↓ fail → error-editing (aria-invalid=true, stays in edit mode)

error-editing
  ↓ user corrects + blur/Enter → validating
  ↓ Esc                         → idle (original value restored)

committed
  ↓ server rollback             → error-toast + revert (see patterns/error-surface.md)
```

The commit is optimistic: the visible value updates locally inside 50ms, the server mutation runs in the background, and a rollback produces an undo toast per `patterns/error-surface.md`. Do not gate the UI on the round trip.

## Stack binding (Base UI)

Base UI does not ship a dedicated InlineEdit primitive. Compose it from `<Field>` + native `<input>` with a local `isEditing` state: render the `<input>` only when `isEditing` is true, otherwise render the value inside a focusable `<button>` or `<div tabIndex={0}>` that carries the "enter edit mode" click/key handlers. Use `@base-ui/react`'s `<Field>` for label/error plumbing (`aria-describedby`, `aria-invalid`). On enter, call `ref.current.focus()` then `ref.current.select()` imperatively — never `autofocus` (`anti-patterns.md § Behavior anti-patterns`). See `stack-bindings/web-primitives.md` for a fuller inline-edit recipe (Task 17 — forward reference).

## Common mistakes

- Using `autofocus` to enter edit mode — fires unpredictably on SPA route changes and steals focus on mount (`anti-patterns.md § Behavior anti-patterns`). Use imperative `focus()` in the transition handler.
- Validating on-change for cheap checks — flashes errors while the user is still typing the value (`patterns/form-validation-timing.md § Rationale`).
- Opening a modal for a single-field edit — violates the "Modal for single-field edits" anti-pattern (`anti-patterns.md § Behavior anti-patterns`); inline-edit is the correct primitive.
- Losing the buffered value when the user Tabs — Tab must commit, not cancel. Esc cancels.
- Leaving focus on `<body>` after the input unmounts — always restore focus to the cell (`keyboard-and-focus.md § On content removed`).

## Principles invoked

- Principle 1 (speed of interaction) — 50ms open budget, optimistic commit.
- Principle 2 (obvious affordances) — hover affordance on pointer, `:focus-visible` on keyboard, full-text selection on entry.
- Principle 3 (keyboard parity) — Enter commits + advances, Esc reverts, F2 for grid users.
- Principle 6 (reversibility) — Esc is a true undo; rollback toast on server failure.
- Principle 7 (respect the user's cursor) — focus returns to the cell on exit, never to `<body>`.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useTextField` — https://react-spectrum.adobe.com/react-aria/useTextField.html
- Notion / Linear inline-edit observed behavior (product inspection, 2026-04).
- `patterns/form-validation-timing.md § Decision table`
- `patterns/error-surface.md`
- `keyboard-and-focus.md § On content removed`, `§ Anti-patterns (from Nordhealth)`
- `response-time-budget.md § Numeric targets`
- `anti-patterns.md § Behavior anti-patterns`
- `foundations.md § Norman's Affordances + Signifiers`
