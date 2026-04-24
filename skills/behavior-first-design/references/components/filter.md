# Filter

A composable filter UI for narrowing a list view (leads, contacts, opportunities) by one or more facets. Active filters render as removable chips; a trailing combobox adds more. Linear-style faceted filtering with keyboard parity and URL-shareable state.

**Composite, not a single primitive.** Per `_decisions-ledger.md § 2026-04-17` (item 1), Filter is one of the six Base UI gaps. Compose from `components/combobox-select.md` + hand-rolled chips + clear-all button. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
keyboard_activation: Enter
esc_closes: yes
validation_timing: n-a
tab_navigates_between_chips: yes
backspace_on_empty_removes_last_chip: yes
individual_chip_remove_button: yes
chip_remove_accessible_name: "Remove filter: <label>"
clear_all_shortcut: explicit-button-only
url_state_serialization: optional
results_debounce_ms: 150
facet_semantics: OR-within-facet-AND-across-facets
empty_state_visible: yes
```

## Keyboard contract

Reproduced from Base UI Combobox (https://base-ui.com/react/components/combobox), react-aria `useTagGroup` / `useComboBox` (https://react-spectrum.adobe.com/react-aria/useTagGroup.html, https://react-spectrum.adobe.com/react-aria/useComboBox.html), and Linear filter bar convention.

| Key | When | Effect |
| --- | --- | --- |
| `Tab` | In filter bar | Move between chips and the trailing combobox |
| `Enter` | Combobox has selection | Commit filter; chip appears; combobox clears |
| `Backspace` | Combobox input is empty | Remove the last chip (Linear parity) |
| `Esc` | Combobox open with query | Clear query buffer, close listbox |
| `Esc` | Combobox input empty, listbox open | Close listbox (do NOT wipe applied chips — use the explicit Clear all button) |
| `Delete` / `Backspace` | Chip focused | Remove that chip; focus moves to next chip (or combobox) |
| `ArrowLeft` / `ArrowRight` | Chip focused | Move focus between chips |
| `Enter` / `Space` | Chip focused | Activate chip edit mode (re-open the combobox preloaded with this facet) — optional |

See `components/combobox-select.md` for the full combobox keyboard map — this file does not re-document it.

## Behavior invariants

- **OR within a facet, AND across facets.** Two `status` chips = `status in [New, Qualified]`; a `status` chip + `owner` chip = `status = New AND owner = Alice`. See `patterns/multi-select.md`. Group chips by facet so the logic is visible.
- **Chip = label + value + remove button.** "Status: New ×" not "New ×". Facet name disambiguates ("New" status vs "New York" city).
- **Individual remove button** with `aria-label="Remove filter: Status is New"`. The × glyph has `aria-hidden="true"`.
- **Clear-all is an explicit button, never a keypress.** Provide a visible "Clear all" link/button in the filter bar. Do NOT bind Esc (or any single key) to clear all chips — Esc only closes the listbox and clears the current query buffer. Do NOT hide Clear all behind a kebab menu.
- **Results debounced 150ms.** Per `response-time-budget.md § Numeric targets` — batches rapid changes. Show a subtle loading indicator (skeleton / `aria-busy="true"`) during debounce, not a blocking spinner.
- **URL state serialization optional but recommended.** Serialize to query string (`?status=new,qualified&owner=alice`). Do NOT JSON-encode (breaks bookmarks).
- **Zero-results empty state** = `components/empty-state.md` variant: "No leads match these filters" + "Clear all" primary action.
- **Filter bar never disappears.** Even when the list is empty, filters stay visible so the user sees the cause.

## State transitions

```
idle (chips present or absent; combobox collapsed and empty; list shows current results)
  ↓ focus combobox | click in chip bar
active (combobox focused; cursor in input; listbox may be open or closed)
  ↓ typing → combobox filtering (see components/combobox-select.md)
  ↓ Enter on selection → chip-adding
chip-adding (new chip inserted; combobox input cleared; listbox closes)
  ↓ 150ms debounce elapses → results-updating
results-updating (list shows loading indicator; filter bar stays interactive)
  ↓ server responds → idle (list updated)

chip removal
  ↓ Backspace on empty combobox | click × on chip | Delete on focused chip
  ↓ chip removed → results-updating (150ms debounce) → idle

clear-all
  ↓ click "Clear all" button (explicit affordance only; no keypress binding)
  ↓ all chips removed → results-updating → idle

no-results (filters yield empty list)
  ↓ render components/empty-state.md variant: "No leads match these filters" + "Clear all" primary action
```

Focus management: adding a chip returns focus to the combobox input (so the next filter can be typed). Removing a chip via its × moves focus to the adjacent chip (or combobox if none left).

## Stack binding (Base UI)

**Composite.** Compose: (1) **Facet + value picker** — Base UI `<Combobox>` (`@base-ui/react/combobox`), either two-step (facet then value) or single combobox with grouped options (faster for power users). (2) **Chips** — hand-rolled; Base UI ships no `<Chip>`. Use a `<button>` with `aria-label` and an inline × (child button, its own `aria-label`). react-aria `useTagGroup` is the semantic reference. (3) **Clear-all** — plain `<button>` / `<a>`. Wrap in `<div role="toolbar" aria-label="Filters">`. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Mega-combobox with no facet/value structure — users can't see OR-within vs AND-across (`anti-patterns.md § Accessibility anti-patterns`).
- × button smaller than 16×16 — Fitts violation (`foundations.md § Fitts's Law`).
- Clearing a chip jumps focus to page top — focus-management bug; keep focus in the filter bar.
- Filter state not in URL — users can't share filtered views; refresh wipes work.
- No debounce — every keystroke fires a server query (`response-time-budget.md § Numeric targets`).
- Empty state without "Clear all" — dead-end for the user (`components/empty-state.md`).
- Chip without facet label — ambiguous when facets share values.
- Backspace-on-empty doesn't remove last chip — breaks Linear/Gmail muscle memory (`voices/linear.md § Actionable principles`).
- Binding Esc to "clear all filters" — reserved for closing the listbox and clearing the query buffer; wiping applied chips via a keypress is a silent-data-loss risk (`anti-patterns.md § Behavior anti-patterns`). Use the explicit Clear all button.

## Principles invoked

- Principle 1 (speed of interaction) — 150ms debounce is a deliberate batching budget, not a latency hit; the bar itself stays instant.
- Principle 2 (obvious affordances) — chips have visible × and labels; clear-all is always reachable.
- Principle 3 (keyboard parity) — Tab between chips, Backspace-on-empty removes last chip, Esc closes listbox / clears query buffer, Enter commits. Mouse parity is implied.
- Principle 4 (progressive disclosure) — idle filter bar shows only the combobox + chips; advanced operators (NOT, OR, regex) live inside a secondary "Advanced" affordance, not the main bar.
- Principle 5 (reversibility) — every chip is one click or one Backspace from removal; clear-all requires an explicit button press (no silent-data-loss keypress).

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Base UI Combobox — https://base-ui.com/react/components/combobox
- react-aria, `useTagGroup` — https://react-spectrum.adobe.com/react-aria/useTagGroup.html
- react-aria, `useComboBox` — https://react-spectrum.adobe.com/react-aria/useComboBox.html
- Linear filter bar pattern — https://linear.app/method/keyboard-shortcuts
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI Filter gap)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Fitts's Law`, `§ Hick's Law`, `§ Jakob's Law`
- `response-time-budget.md § Numeric targets` (150ms debounce band)
- `patterns/multi-select.md` (OR-within, AND-across)
- `voices/linear.md § Actionable principles`
- `components/combobox-select.md`, `components/empty-state.md`, `components/button.md`
- `anti-patterns.md § Accessibility anti-patterns`, `§ Behavior anti-patterns`
