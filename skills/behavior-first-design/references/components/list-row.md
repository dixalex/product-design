# List Row

A single row in a virtualized list of entities (leads, contacts, opportunities, tasks). The atomic unit of the CRM list view: surfaces data, exposes inline actions on hover, participates in keyboard nav, and is the handle for single- and multi-select. Where power users spend most of their time — must feel instant, keyboard-first, and quiet.

**No primitive in any library — hand-rolled.** Per `_decisions-ledger.md § 2026-04-17` (item 1), ListRow is one of the six Base UI gaps; no canonical third-party fallback either (react-aria's `useGridList` is the closest semantic reference). See `keyboard-and-focus.md § Listbox / List row` and `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: yes
keyboard_activation: both
esc_closes: n-a
validation_timing: n-a
role_container: listbox
role_item: option
aria_selected_on_selected_row: yes
roving_tabindex: yes
jk_navigation: yes
arrow_navigation: yes
home_end_navigation: yes
space_toggles_peek: yes
enter_opens_detail: yes
hover_reveal_latency_ms: 16
pointerleave_hide_actions: immediate
inline_actions_max: 4
overflow_menu_when_over: yes
row_min_height_px: 36
row_selection_animation: none
jk_bound_only_inside_listbox: yes
```

## Keyboard contract

Reproduced from react-aria `useGridList` / `useListBox` (https://react-spectrum.adobe.com/react-aria/useGridList.html, https://react-spectrum.adobe.com/react-aria/useListBox.html), WAI-ARIA Listbox pattern (https://www.w3.org/WAI/ARIA/apg/patterns/listbox/), and Linear/Gmail observed convention. Both vim-style (`j`/`k`) and standard (`ArrowDown`/`ArrowUp`) are active simultaneously per `_decisions-ledger.md § 2026-04-17` item 5.

| Key | When | Effect |
| --- | --- | --- |
| `j` / `ArrowDown` | Row focused | Move focus to next row (stops at last; no wrap) |
| `k` / `ArrowUp` | Row focused | Move focus to previous row (stops at first) |
| `Home` | Row focused | Jump to first row |
| `End` | Row focused | Jump to last row |
| `PageDown` / `PageUp` | Row focused | Jump one viewport of rows |
| `Space` | Row focused | Toggle peek (inline detail expansion) per `patterns/multi-select.md` |
| `Enter` | Row focused | Open detail view (navigate / push panel) |
| `⌘+Click` / `Ctrl+Click` | Row | Toggle selection (multi-select) per `patterns/multi-select.md` |
| `Shift+Click` | Row | Range-select from anchor per `patterns/multi-select.md` |
| `x` | Row focused (optional, Gmail-parity) | Toggle row selection checkbox |
| `Tab` | Anywhere in list | Exit the list — one Tab stop per list (roving tabindex) |

**j/k binding scope:** j/k fire only when focus is inside the `role="listbox"` subtree AND not in an editable element (`input`, `textarea`, `[contenteditable]`). Otherwise typing "j" in an inline-edit moves the row. See `keyboard-and-focus.md § Listbox / List row`.

## Behavior invariants

- **Hover reveals actions within one frame (≤16ms).** Per `response-time-budget.md § Numeric targets`, the tightest latency budget in the product. No animation; direct `opacity` or `display` swap. No transition duration.
- **Pointerleave hides actions immediately.** No 200ms grace period; no ghost-action trail.
- **No motion on row selection or keyboard nav.** Per `motion.md § The frequency-and-novelty matrix` and `voices/rauno-mechanics.md § Actionable principles (principle 1)` — highest-frequency interactions; animating them creates noise.
- **Roving tabindex, not one-per-row tabindex.** Exactly one row has `tabindex="0"`; others are `-1`. The entire list is a single Tab stop (WAI-ARIA composite widget rule).
- **`aria-selected="true"` on selected rows**, `aria-activedescendant` (optional) on container for screen-reader announcement.
- **Inline actions ≤4** per `foundations.md § Miller's Law` (Cowan ~4). More → overflow menu (`⋯`).
- **Row min-height 36px** (32px in dense mode, 44px touch) per `foundations.md § Fitts's Law`.
- **Peek ≠ detail view.** Space toggles inline expansion; Enter opens full detail. Two distinct affordances, two distinct keys.
- **Auto-focus on mount.** When the listbox is the page's dominant surface, the container auto-focuses on mount via `containerRef.current?.focus({ preventScroll: true })`. Users arrive on a list expecting keys to work without a priming click (Linear / Gmail / Superhuman parity per `voices/linear.md § Actionable principles`). Skip when a dialog/modal is open above the list or when a focus-management ancestor (e.g. form wizard) owns initial focus.
- **Unified cursor: mouse and keyboard share one focus state.** On `mousedown` inside the listbox, the row under the pointer takes the roving keyboard focus index BEFORE the row's own click fires. After a click that lands on a focusable child (e.g. an action button), re-focus the listbox so subsequent j/k continue working. Mouse and keyboard are two inputs to one cursor — never two cursors.

## State transitions

```
idle (default; actions hidden; row background = transparent)
  ↓ pointerenter (within 16ms)
hovered (actions visible in trailing slot; background = --row-hover)
  ↓ pointerleave (immediate; no delay)
idle
  ↓ j | k | ArrowUp | ArrowDown | Home | End (focus moves here)
focused (selection ring visible; actions visible as if hovered; roving tabindex = 0)
  ↓ Space
active-peek (inline expansion open below row; focus stays on row)
  ↓ Space again | Esc | focus leaves row
focused
  ↓ Enter
active-detail (detail view opens — navigation or side panel; row stays marked as last-visited)
  ↓ close detail
focused (last-visited row retains focus per keyboard-and-focus.md § On dialog open / close)
  ↓ ⌘+Click | x key | checkbox toggle
selected (aria-selected=true; selection stripe / checkbox checked; stays in current pointer state — hovered or idle)
  ↓ ⌘+Click again → unselected
```

Selection and focus are **independent axes**: a row can be focused-and-unselected, focused-and-selected, unfocused-and-selected. Cross-ref `patterns/multi-select.md` for Cmd/Shift+click semantics and the selection-anchor model.

## Stack binding (Base UI)

**No primitive in any library.** Hand-roll as `<div role="option">` inside `<div role="listbox">` (or `row`/`grid` for multi-column with cell-level focus). Use react-aria `useListBox`/`useGridList` as a **semantic reference** — copy the keyboard + roving-tabindex logic even without adopting the hooks. For lists over ~100 items (per `voices/eagle.md § Actionable principles (principle 6)`), virtualize with `@tanstack/react-virtual`. Roving tabindex and focus management are orthogonal — the virtualizer manages DOM mount/unmount, not the focus contract. Peek = Base UI `<Collapsible>`; inline actions = Base UI `<Menu.Trigger>` icons. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Always-visible action strip — defeats hover-reveal, adds noise (`anti-patterns.md § Accessibility anti-patterns`).
- 200ms hover delay — blows the 16ms budget (`response-time-budget.md § Numeric targets`).
- Animating row bg on keyboard nav — violates `motion.md § The frequency-and-novelty matrix`.
- j/k firing inside inline-edit input — row jumps while typing (`keyboard-and-focus.md § Listbox / List row`).
- Every row a Tab stop — 200 Tab presses to escape. Use roving tabindex.
- Enter opens peek AND detail — violates one-key-one-action. Space = peek, Enter = detail.
- Row-click selects (instead of opens) — breaks "click opens" expectation (`patterns/multi-select.md`).
- Ghost actions after pointerleave — stale hover; hide immediately.
- More than 4 inline actions with no overflow — violates `foundations.md § Miller's Law`.
- Missing `aria-selected` — multi-select invisible to screen readers (`anti-patterns.md § Accessibility anti-patterns`).

## Principles invoked

- Principle 1 (speed of interaction) — hover reveal ≤16ms; no keyboard-nav motion.
- Principle 2 (obvious affordances) — hover-reveal signals "actions live here"; selection ring signals focus.
- Principle 3 (keyboard parity) — j/k and arrow keys both active; one Tab stop per list via roving tabindex.
- Principle 4 (progressive disclosure) — idle row is quiet; hover reveals actions; peek reveals detail; Enter reveals the full view.
- Principle 5 (reversibility) — selection toggles; peek toggles; no destructive action fires from a row interaction.
- Principle 7 (respect the user's cursor) — pointerleave hides actions instantly; no ghosts, no sticky hover.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useGridList` — https://react-spectrum.adobe.com/react-aria/useGridList.html
- react-aria, `useListBox` — https://react-spectrum.adobe.com/react-aria/useListBox.html
- WAI-ARIA Listbox pattern — https://www.w3.org/WAI/ARIA/apg/patterns/listbox/
- WAI-ARIA Grid pattern (for multi-column rows) — https://www.w3.org/WAI/ARIA/apg/patterns/grid/
- Linear issue-list keyboard conventions — https://linear.app/method/keyboard-shortcuts
- Gmail conversation-list shortcuts — https://support.google.com/mail/answer/6594
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI ListRow gap), item 5 (j/k + arrow parity)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Miller's Law`, `§ Fitts's Law`, `§ Jakob's Law`
- `response-time-budget.md § Numeric targets` (16ms hover reveal)
- `motion.md § The frequency-and-novelty matrix`
- `keyboard-and-focus.md § Listbox / List row`
- `patterns/multi-select.md`
- `voices/linear.md § Actionable principles`, `voices/rauno-mechanics.md § Actionable principles (principle 1)`
- `anti-patterns.md § Accessibility anti-patterns`, `§ Behavior anti-patterns`
- `components/checkbox.md`, `components/dropdown-menu.md`, `components/popover.md`
