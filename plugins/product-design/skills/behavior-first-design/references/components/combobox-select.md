# Combobox / Select

A text input (combobox) or trigger (select) that opens a listbox of options; the user picks one via typing, arrows, or pointer. Use combobox when options benefit from filter-as-you-type; use select when the list is short and the set is closed.

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: n-a
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: yes
validation_timing: on-blur
typeahead: yes
auto_scroll_during_keyboard_nav: yes
disabled_items_skipped: yes
focus_returns_to_trigger_on_close: yes
remote_search_debounce_ms: 300
```

## Keyboard contract

Reproduced from Radix Primitives (https://www.radix-ui.com/primitives/docs/components/select#keyboard-interactions), Base UI Combobox/Select (https://base-ui.com/react/components/combobox), and react-aria `useComboBox` (https://react-spectrum.adobe.com/react-aria/useComboBox.html). Select and Combobox differ chiefly in whether the trigger accepts typing; keyboard navigation inside the open listbox is identical.

| Key | When | Effect |
| --- | --- | --- |
| `Space` / `Enter` | Trigger focused, closed | Open listbox; focus first item (or selected, if any) |
| `ArrowDown` | Trigger focused, closed | Open listbox; focus first item |
| `ArrowUp` | Trigger focused, closed | Open listbox; focus last item |
| `ArrowDown` / `ArrowUp` | Listbox open | Move focus to next / previous enabled item; scroll into view |
| `Home` / `End` | Listbox open | Focus first / last enabled item |
| `PageDown` / `PageUp` | Listbox open | Move focus by viewport page |
| `Enter` | Item focused | Select item, close listbox, return focus to trigger |
| `Space` | Item focused (Select only) | Select item, close listbox |
| `Esc` | Listbox open | Close listbox, return focus to trigger, no selection change |
| `Tab` | Listbox open | Close listbox, commit focused selection, advance focus (Combobox) / no selection (Select) |
| Typeahead (printable chars) | Listbox open or trigger focused | Focus first option whose label starts with the typed prefix (case-insensitive); repeated keystrokes extend the buffer (~500ms window) |
| Typing into input | Combobox only | Filter options; listbox opens automatically; first match focused |

Auto-scroll: as ArrowDown/Up moves focus, the listbox viewport scrolls so the focused item is always visible. Keyboard users must never lose their cursor off-screen.

## Behavior invariants

- **Typeahead** — closed Select: typing focuses the matching option silently. Combobox: typing opens the listbox and filters.
- **Disabled items skipped by arrow nav** but remain in DOM for AT. Never let ArrowDown land on a disabled row.
- **Focus returns to trigger on every close path** — Esc, Enter-selection, click-outside, or Tab (`keyboard-and-focus.md § On dialog open / close`).
- **Remote search debounces on-change 300ms** (`patterns/form-validation-timing.md § Decision table`); skeleton only if results >200ms (`response-time-budget.md § Perceived-performance techniques`).
- **Selected item has text/icon + color** and `aria-selected="true"` — color alone fails (`anti-patterns.md § Behavior anti-patterns`).
- **Explicit no-results state** — "No matches" text, never a silent blank list.
- **Virtualize past 50 items** (`anti-patterns.md § Performance anti-patterns`) or blow the 16ms frame budget.

## State transitions

```
idle (trigger focused or unfocused)
  ↓ Enter | Space | ArrowDown | ArrowUp | click
open (listbox mounted)
  ↓ ArrowDown/Up | typeahead
    → focused-item (different item; scrolled into view)
  ↓ typing (combobox only)
    → filtering (debounced 300ms for remote) → open | no-results
    no-results → filtering (on further typing — buffer changes)
  ↓ Enter on item
    → selected → closing → idle (focus on trigger)
  ↓ Esc | click-outside | Tab
    → closing → idle (focus on trigger; selection unchanged)

loading (remote search in flight)
  ↓ data arrives → open
  ↓ error → error-state (inline message, retry affordance)
```

Open paints within 100ms (`response-time-budget.md § Numeric targets` — analogous to dialog-open). Item focus change within a frame (16ms).

## Stack binding (Base UI)

Use `<Select.Root>` + `<Select.Trigger>` + `<Select.Popup>` + `<Select.Item>` for a closed fixed set; use `<Combobox.Root>` + `<Combobox.Input>` + `<Combobox.Popup>` for filter-as-you-type. Both live in `@base-ui/react`. Base UI owns keyboard nav, typeahead, and focus return; you own the option data and the (optional) remote-search debounce. For remote search, drive `<Combobox.Input>` with controlled `value` + `onValueChange`, and render `<Combobox.Empty>` for the no-results state. Wrap the Popup in your own portal if you need custom z-index stacking — Base UI uses the Floating UI positioning stack so collision detection is already handled. See `stack-bindings/web-primitives.md` for virtualization integration (Task 17 — forward reference).

## Common mistakes

- ArrowDown lands on a disabled item — always skip to next enabled (`anti-patterns.md § Accessibility anti-patterns`).
- Focused item scrolls out of view during keyboard nav — users cannot track their cursor.
- Blank no-results state — render explicit "No matches" text.
- Color-only selected indicator (`anti-patterns.md § Behavior anti-patterns`).
- Debounce <300ms on remote search (`patterns/form-validation-timing.md § Rationale`).
- Not virtualizing a 500-item list (`anti-patterns.md § Performance anti-patterns`).
- >10 items without search — use Combobox instead of Select (`foundations.md § Hick's Law`).

## Principles invoked

- Principle 1 (speed of interaction) — open <100ms, keyboard nav <16ms, debounce 300ms.
- Principle 2 (obvious affordances) — selected state has text/icon + color, focused item always visible.
- Principle 3 (keyboard parity) — typeahead, arrows, Home/End, PageUp/Down, Esc, Tab commit.
- Principle 4 (information scent / progressive disclosure) — filter-as-you-type surfaces relevant options without forcing a scroll.
- Principle 7 (respect the user's cursor) — focus returns to trigger on every close path.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useComboBox` — https://react-spectrum.adobe.com/react-aria/useComboBox.html
- Radix Primitives, Select keyboard interactions — https://www.radix-ui.com/primitives/docs/components/select#keyboard-interactions
- Base UI, Combobox — https://base-ui.com/react/components/combobox
- Base UI, Select — https://base-ui.com/react/components/select
- `keyboard-and-focus.md § On dialog open / close` (focus-return doctrine)
- `patterns/form-validation-timing.md § Decision table`
- `response-time-budget.md § Numeric targets`, `§ Perceived-performance techniques`
- `anti-patterns.md § Behavior anti-patterns`, `§ Performance anti-patterns`
- `foundations.md § Hick's Law`
