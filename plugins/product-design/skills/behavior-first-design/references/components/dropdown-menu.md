# Dropdown menu

A trigger that opens a transient menu of commands. Each item invokes an action (not a selection — use Combobox/Select for that). Supports nested submenus, separators, checkbox and radio items.

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both
esc_closes: yes
validation_timing: n-a
typeahead: yes
submenu_open_direction: right
focus_strategy_on_open: first
esc_returns_focus_to_trigger: yes
disabled_items_skipped: yes
close_reason_tracked: yes
```

## Keyboard contract

Reproduced from Radix Primitives (https://www.radix-ui.com/primitives/docs/components/dropdown-menu#keyboard-interactions) with cross-reference to react-aria `useMenu` Features (https://react-spectrum.adobe.com/react-aria/useMenu.html). Also mirrored in `keyboard-and-focus.md § Menu / Dropdown`.

| Key | When | Effect |
| --- | --- | --- |
| `Space` / `Enter` | Trigger focused, menu closed | Open menu, focus first item |
| `ArrowDown` | Trigger focused, menu closed | Open menu, focus first item |
| `ArrowUp` | Trigger focused, menu closed | Open menu, focus last item |
| `ArrowDown` / `ArrowUp` | Menu open | Move focus to next / previous enabled item, wrapping |
| `Home` / `End` | Menu open | Focus first / last enabled item |
| `Enter` / `Space` | Item focused | Activate item, close menu |
| `ArrowRight` | Submenu trigger focused | Open submenu, focus first item |
| `ArrowLeft` | Inside submenu | Close submenu, return focus to parent submenu trigger |
| `Esc` | Menu open | Close menu (and any open submenus), return focus to root trigger |
| `Tab` | Menu open | Close menu, advance focus normally to next page focusable |
| `Shift` + `Tab` | Menu open | Close menu, move focus to previous page focusable |
| Typeahead (printable chars) | Menu open | Focus first item whose label starts with the typed prefix (~500ms buffer window) |

## Behavior invariants

- **Submenu open direction respects collision** — default right; flip left if viewport would clip. Users should never scroll a submenu into view.
- **First item focused on ArrowDown/Enter/Space**; last on ArrowUp. The trigger key communicates direction of intent.
- **Disabled items skipped by keyboard nav** but remain in DOM for AT. Never let ArrowDown land on a disabled row.
- **Esc closes everything and returns focus to the root trigger** — even from three levels deep. A single Esc is the universal "get me out."
- **Tab and Shift+Tab close and advance normally** — the menu is not a Tab stop. This is the key difference from Dialog, which traps Tab.
- **Close reason is tracked** — per Base UI's `ChangeEventReason` enum (12 values): `trigger-press`, `outside-press`, `escape-key`, `item-press`, `focus-out`, `list-navigation`, `cancel-open`, `close-press`, `sibling-open`, `window-resize`, `scroll`, `item-selection`. Knowing *why* the menu closed lets the caller suppress immediate re-open on `outside-press`, log telemetry, etc.

## State transitions

```
idle (trigger focused or unfocused)
  ↓ Enter | Space | ArrowDown → open-first-focused
  ↓ ArrowUp                    → open-last-focused
  ↓ pointer click              → open-no-focus (no item highlighted until hover/key)

open
  ↓ ArrowDown/Up | typeahead   → focused-item (next/prev enabled, wrapping)
  ↓ ArrowRight on submenu-trigger → submenu-open (focus first submenu item)
  ↓ ArrowLeft in submenu        → parent-menu (focus on submenu trigger)
  ↓ Enter | Space on item       → activate → closing → idle (focus on trigger)
  ↓ Esc                          → closing (reason=escape-key) → idle (focus on trigger)
  ↓ click outside                → closing (reason=outside-press) → idle
  ↓ Tab | Shift+Tab              → closing (reason=focus-out) → advance focus
```

Open paints within 100ms (`response-time-budget.md § Numeric targets`). Submenu hover-intent opens at ≤300ms (Radix / react-aria default — short enough to feel responsive, long enough to allow diagonal mouse paths across parent items without firing the wrong submenu). Keyboard `ArrowRight` opens immediately with no delay.

## Stack binding (Base UI)

Use `<Menu.Root>` + `<Menu.Trigger>` + `<Menu.Popup>` + `<Menu.Item>` from `@base-ui/react`. For submenus, nest `<Menu.Root>` + `<Menu.SubmenuTrigger>` + `<Menu.Popup>`. For checkbox and radio items, use `<Menu.CheckboxItem>` and `<Menu.RadioItem>` — these handle `aria-checked` and keep keyboard nav clean. Hook the `onOpenChange` handler with `(open, event, reason) => …` to inspect the `ChangeEventReason` and decide whether to re-focus a different element, log telemetry, or suppress a re-open. Base UI handles typeahead, focus strategy, and collision-aware placement. See `stack-bindings/web-primitives.md` for Radix/react-aria equivalents (Task 17 — forward reference).

## Common mistakes

- Using a dropdown menu for selection (one of N values) instead of a Select — menu items are *commands*, not values. Switch to `components/combobox-select.md`.
- Leaving the menu as a Tab stop (`tabindex="0"` on the Popup) — Tab must close, not cycle. Positive `tabindex` values are banned (`anti-patterns.md § Behavior anti-patterns`).
- Spring/bounce open animation — violates `anti-patterns.md § Behavior anti-patterns` (no bouncing in product UI).
- Animation duration >300ms — violates `anti-patterns.md § Behavior anti-patterns`. Default menu transition is 150ms (`motion.md § Duration defaults`).
- Disabled items reachable by ArrowDown — skip them; AT users lose their place if arrow focus lands on an inert row.
- Hick's Law violation: >10 flat items without grouping — add separators and sub-sections, or promote heavy items to a dialog (`foundations.md § Hick's Law`).

## Principles invoked

- Principle 1 (speed of interaction) — open <100ms, keyboard nav <16ms, transition ≤150ms.
- Principle 2 (obvious affordances) — `:focus-visible` on items, icons have names, disabled state visibly distinct.
- Principle 3 (keyboard parity) — full contract above; Enter/Space/Arrows/Home/End/Esc/Tab all behave.
- Principle 4 (information scent) — submenus group related commands; typeahead collapses long lists.
- Principle 7 (respect the user's cursor) — Esc from any depth returns focus to root trigger.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Radix Primitives, DropdownMenu keyboard interactions — https://www.radix-ui.com/primitives/docs/components/dropdown-menu#keyboard-interactions
- react-aria, `useMenu` — https://react-spectrum.adobe.com/react-aria/useMenu.html
- Base UI, Menu — https://base-ui.com/react/components/menu (for `ChangeEventReason` taxonomy)
- `keyboard-and-focus.md § Menu / Dropdown` (parallel table, same source)
- `keyboard-and-focus.md § Principles`, `§ Anti-patterns (from Nordhealth)`
- `response-time-budget.md § Numeric targets`
- `motion.md § Duration defaults`
- `anti-patterns.md § Behavior anti-patterns`
- `foundations.md § Hick's Law`
