# Command Palette

A ⌘K-summoned modal launcher that lets the user search and execute any action in the product by typing its name. The primary surface for Linear-class keyboard-first workflows; the place where power users live.

**Base UI has NO Command Palette primitive.** Per `_decisions-ledger.md § 2026-04-17` (item 1), CommandPalette is one of the six Base UI gaps. Use **`cmdk`** (by pacocoursey, the library behind Linear, Vercel, Raycast-for-web) as the **primary stack**. See `stack-bindings/web-primitives.md` (Task 17 — forward reference) for the wiring.

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
keyboard_activation: Enter
esc_closes: yes
esc_goes_back_in_sub_pane: yes
validation_timing: n-a
summon_shortcut: cmd+k
summon_shortcut_mac_and_windows: yes
typeahead_match_type: substring
recents_shown_before_typing: yes
top_list_visible_count: 4
sub_commands_supported: yes
palette_first_paint_ms: 50
enter_motion_ms: 120
reduced_motion_enter_ms: 0
trap_focus: yes
portal_to_body: yes
```

## Keyboard contract

Reproduced from cmdk docs (https://cmdk.paco.me/) and Raycast keyboard conventions (https://developers.raycast.com/api-reference/user-interface/navigation). The palette is a **text input + filtered listbox** composite, not a menu.

| Key | When | Effect |
| --- | --- | --- |
| `⌘K` / `Ctrl+K` | Anywhere | Open palette; focus → input; preselect first command |
| `Esc` | Palette open, root pane | Close; focus → previously-focused element |
| `Esc` / `Backspace` on empty input | Sub-pane open | Pop back to parent pane |
| Typing | Input focused | Substring filter (not prefix); highlight matches |
| `ArrowDown` / `ArrowUp` | Input focused | Move selection through filtered list (wraps) |
| `Home` / `End` | Input focused | First / last visible command |
| `Enter` | Command selected, leaf | Execute; close palette |
| `Enter` | Command selected, has sub-actions | Push sub-pane (do NOT close) |
| `⌘+Enter` | Command selected | Execute in background (keep palette open) — optional |
| `Tab` | Palette open | No effect — focus is trapped on input |

Selection moves on arrow keys only; pointer hover does NOT hijack keyboard selection (cmdk default, Raycast parity — avoids the "my cursor stole my arrow-key spot" bug).

## Behavior invariants

- **Substring filter, fuzzy-match-friendly.** Not prefix-only. "new lead" matches "Create New Lead" and "Add lead (new)". cmdk exposes `shouldFilter` + custom `filter` function; default scoring is substring + fuzzy.
- **Recents / suggested before typing.** Empty-input state shows ≤4 "recently used" + ≤4 "suggested" commands, grouped. Keeps first-paint list under `foundations.md § Miller's Law` (Cowan ~4).
- **No scroll on first paint.** If the filtered list exceeds viewport, paginate by scroll; never open with a scrollbar visible.
- **First paint ≤50ms after ⌘K.** Per `response-time-budget.md § Numeric targets` — this is a critical path. Preload the command registry; don't fetch on open.
- **Sub-pane pattern.** A command flagged `command_has_sub_actions: true` pushes a child list on Enter (e.g., "Change status →" → [New, Contacted, Qualified, Won, Lost]). The input clears; placeholder updates to the sub-context ("Select status…"). Esc/Backspace-on-empty pops.
- **Input never loses focus while open.** Clicking a row executes it; the input stays focused throughout so typing continues to filter.
- **Loading rows are inert.** If a command is async-loaded (recent items from the server), show a skeleton row with `aria-busy="true"` — not a blocking spinner over the whole palette.
- **Announce filter result count** via `aria-live="polite"` on the listbox wrapper so screen readers hear "14 results" after a typing pause.

## State transitions

```
closed (palette unmounted; global ⌘K listener armed)
  ↓ ⌘K | Ctrl+K | programmatic open
opening (120ms enter; backdrop fades; scale 0.98 → 1)
  ↓ animation ends (or instantly if prefers-reduced-motion)
open-default (input empty; recents + suggested visible; selection = first row)
  ↓ typing → filtering
filtering (debounced 0ms — cmdk filters synchronously; results update per keystroke)
  ↓ input cleared → open-default
  ↓ Enter on leaf command → executing
  ↓ Enter on parent command → sub-pane-open
executing (command fires; palette closes unless ⌘+Enter modifier)
  ↓ → closed; focus returns to the pre-open element
sub-pane-open (parent command's children as new list; input placeholder updated)
  ↓ Esc | Backspace-on-empty → open-default | previous sub-pane
  ↓ Enter on child → executing
```

Focus returns on close per `keyboard-and-focus.md § On dialog open / close`. Reduced-motion collapses opening/sub-pane transitions to instant swaps (`motion.md § prefers-reduced-motion`).

## Stack binding (Base UI)

**Base UI has no Command Palette primitive.** Use **`cmdk`** (pacocoursey). It provides `<Command>`, `<Command.Input>`, `<Command.List>`, `<Command.Item>`, `<Command.Group>`, and handles filter + selection + keyboard routing out of the box. Mount it inside a Base UI `<Dialog>` for the modal shell (focus trap, scroll lock, `Esc` handler, portal) — this keeps palette layering consistent with the rest of the dialog stack in the app. Sub-panes: render a different `<Command.List>` based on a `pane` state variable; reset input value when pushing. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Opening a palette with >4 default rows visible — burns `foundations.md § Miller's Law`. Trim recents + suggested to ≤4 each.
- Prefix-only filter — "send email" fails to match "Email a contact". Use substring/fuzzy (Raycast convention).
- Executing on hover — breaks keyboard parity; mouse becomes the canonical input (`anti-patterns.md § Behavior anti-patterns`).
- Autofocusing a row instead of the input — typing immediately becomes a shortcut, not a filter.
- No Esc-to-go-back in sub-panes — users get stuck and tab away (`anti-patterns.md § Behavior anti-patterns`).
- Open animation >200ms — palette feels like a modal, not a summon (`response-time-budget.md § Numeric targets`).
- Fetching the command list on ⌘K — the first paint misses the 50ms budget; preload at mount.
- No announcement of result count — screen readers get no signal that filtering happened.

## Principles invoked

- Principle 1 (speed of interaction) — ⌘K-to-first-paint ≤50ms; typing filters synchronously.
- Principle 2 (obvious affordances) — clear input placeholder ("Type a command or search…"); selected row has visible highlight + icon; recents grouped with a header.
- Principle 3 (keyboard parity) — entire palette navigable without mouse; sub-pane pop via Esc is keyboard-first.
- Principle 4 (progressive disclosure) — sub-panes let complex commands (status change, assign-to-user) hide 5+ options behind one parent row.
- Principle 5 (reversibility) — Esc closes without side effects; ⌘+Enter variant keeps palette open so you can try the next command.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- cmdk (pacocoursey) — https://cmdk.paco.me/
- Raycast "Navigation" API — https://developers.raycast.com/api-reference/user-interface/navigation
- Raycast "Extensions" design essay — https://www.raycast.com/blog/how-raycast-extensions-work
- Linear's command menu pattern — https://linear.app/method/keyboard-shortcuts
- WAI-ARIA Combobox pattern (closest analogue) — https://www.w3.org/WAI/ARIA/apg/patterns/combobox/
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI CommandPalette gap)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Miller's Law` (Cowan ~4), `§ Jakob's Law`, `§ Hick's Law`
- `response-time-budget.md § Numeric targets` (50ms first paint)
- `motion.md § Duration defaults`, `§ prefers-reduced-motion`
- `keyboard-and-focus.md § On dialog open / close`
- `voices/linear.md § Actionable principles`
- `anti-patterns.md § Behavior anti-patterns`
- `components/dialog.md`, `components/dropdown-menu.md` (layer hierarchy)
