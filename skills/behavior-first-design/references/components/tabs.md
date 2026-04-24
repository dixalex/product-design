# Tabs

A set of mutually-exclusive panels with a controls strip that switches between them. Use for parallel views over the same subject (e.g., a lead's *Overview / Activity / Emails / Tasks*) — not for unrelated pages (use a sidebar / nav) and not for sequenced flows (use a stepper / wizard).

Base UI ships `@base-ui/react/tabs` — use it. Hand-rolling is a common source of WAI-ARIA drift (forgetting `Home`/`End`, forgetting roving tabindex on the list).

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
hover_affordance_hidden_in_idle: no
keyboard_activation: both
esc_closes: n-a
validation_timing: n-a
orientation: horizontal   # or vertical
activate_on_focus: yes    # automatic activation per WAI-ARIA unless content loads async
loop_focus: no            # Home/End jump but Arrow does not wrap (WAI-ARIA default)
roving_tabindex: yes      # owned by Base UI
selected_has_aria_selected: yes
keep_mounted: no          # unless panel state is expensive to rebuild
motion_on_switch: none    # high-freq, low-novelty — 0ms per `motion.md`
```

## Keyboard contract

Reproduced from WAI-ARIA Tabs pattern (https://www.w3.org/WAI/ARIA/apg/patterns/tabs/) and Base UI Tabs docs (https://base-ui.com/react/components/tabs). Base UI's `<Tabs.List>` owns the roving tabindex; only the active tab is in the page tab sequence.

| Key | When | Effect |
| --- | --- | --- |
| `Tab` | Focus before the tab list | Move focus onto the currently-selected tab (one list-level tab stop) |
| `Tab` | Focus on a tab | Move focus out of the list into the active panel's first focusable (or past it if empty) |
| `ArrowRight` | Tab focused, horizontal orientation | Move focus to next tab; if `activateOnFocus`, also activate |
| `ArrowLeft` | Tab focused, horizontal | Move focus to previous tab |
| `ArrowDown` | Tab focused, vertical orientation | Move focus to next tab |
| `ArrowUp` | Tab focused, vertical | Move focus to previous tab |
| `Home` | Tab focused | Move focus to first tab |
| `End` | Tab focused | Move focus to last tab |
| `Enter` / `Space` | Tab focused, `activateOnFocus={false}` | Activate the focused tab (manual activation) |
| `Enter` / `Space` | Tab focused, `activateOnFocus={true}` | No-op — already active |

Two activation modes per WAI-ARIA: *automatic activation* (tab activates when focus moves) is the default and matches the user's intent to browse siblings. *Manual activation* (user must press Enter/Space) is for cases where switching tabs is expensive — async-loaded panels, unsaved-form-state warnings. Base UI exposes both via the `<Tabs.List activateOnFocus>` prop. Pine's default: `activateOnFocus={true}` (automatic). Switch to manual when the Panel does async work on activation.

Loop behavior: WAI-ARIA permits either wrap-at-end or stop-at-end for Arrow keys. Pine's default: stop-at-end (`loopFocus={false}`) — matches list-row keyboard model (`keyboard-and-focus.md § Listbox / List row`) where stops avoid surprise teleports. Use `loopFocus={true}` only for tab strips with 3 or fewer tabs where the loop is always visible.

## Behavior invariants

- **Exactly one tab in the tab sequence.** Roving tabindex means only the active tab carries `tabindex="0"`; others are `-1`. Tab key enters and exits the list as a single stop. Base UI owns this — don't override tabindex.
- **`aria-selected="true"` on the active tab**, `role="tab"` on each control, `role="tablist"` on the container, `role="tabpanel"` on each panel with matching `aria-labelledby` → tab id. Base UI emits all of this.
- **Orientation must be declared.** Pass `orientation="vertical"` on `<Tabs.Root>` when tabs stack — this changes the arrow-key axis (`ArrowUp`/`ArrowDown` instead of `ArrowLeft`/`ArrowRight`). Wrong orientation = broken keyboard.
- **No motion on tab switch** — high-frequency, low-novelty interaction per `motion.md § The frequency-and-novelty matrix`. Panel swaps are instant. The animated indicator rail (if used) is decorative unless it communicates direction; keep it to a ≤100ms transform transition or skip it entirely.
- **Panels are unmounted on switch by default.** Use `keepMounted={true}` on `<Tabs.Panel>` only when panel state is expensive to rebuild (form with local state, heavy virtualized list). Keeping all panels mounted multiplies DOM weight by panel count.
- **Async panel loading announces via live region**, not by freezing the tab strip. If data for the new panel takes >100ms, render a skeleton inside the panel; don't delay the tab-switch itself (`patterns/skeleton-vs-spinner.md`).
- **Disabled tabs are keyboard-reachable but not activatable** — Base UI's `<Tabs.Tab disabled>` handles this; ensure visual treatment signals "known but unavailable" rather than "hidden."

## State transitions

```
tab_a selected
  ↓ click tab_b | ArrowRight (auto-activate) | Home/End
switching (synchronous state update — no animation)
  ↓
tab_b selected (tab_a panel unmounted unless keepMounted; tab_b panel mounted)
  ↓ async data needed for tab_b
loading (panel shows skeleton; tab strip stays interactive)
  ↓ data resolves
tab_b ready

manual activation mode:
  ↓ ArrowRight moves focus only
  ↓ Enter/Space activates; otherwise focus remains on unselected tab
```

## Stack binding (Base UI)

Use `<Tabs.Root>` + `<Tabs.List>` + `<Tabs.Tab value=...>` (one per tab) + `<Tabs.Panel value=...>` (one per tab) from `@base-ui/react/tabs`. Drive `value` / `onValueChange` for controlled mode; `defaultValue` for uncontrolled. Orientation via `orientation` on `<Tabs.Root>`. Activation mode via `activateOnFocus` on `<Tabs.List>`. Roving tabindex, arrow-key navigation, Home/End, and ARIA attributes are all owned by the primitive. See `stack-bindings/web-primitives.md § Per-component binding` for the library row.

```tsx
// Minimal composition (sketch):
// <Tabs.Root defaultValue="overview">
//   <Tabs.List activateOnFocus>
//     <Tabs.Tab value="overview">Overview</Tabs.Tab>
//     <Tabs.Tab value="activity">Activity</Tabs.Tab>
//   </Tabs.List>
//   <Tabs.Panel value="overview">…</Tabs.Panel>
//   <Tabs.Panel value="activity">…</Tabs.Panel>
// </Tabs.Root>
```

## Common mistakes

- Tabs used for sequential steps — use a stepper; tabs imply parallel views over the same subject.
- Tabs used for top-level navigation between pages — use a sidebar / nav bar. Tabs belong inside a view, not above views.
- `orientation` mismatched to visual axis — keyboard arrows then feel broken (pressing Right on a vertical strip does nothing).
- Every panel kept mounted — memory bloat; set `keepMounted` per-panel only when needed.
- Positive `tabindex` on tabs to "reorder" — breaks the roving model (`keyboard-and-focus.md § Anti-patterns`).
- Animating the panel swap (fade, slide) — violates `motion.md § The frequency-and-novelty matrix` for a high-freq interaction.
- Missing `aria-selected` / `role="tabpanel"` because the designer hand-rolled — use Base UI's primitive; ARIA drift is the #1 hand-roll failure.

## Principles invoked

- Principle 1 (speed of interaction) — tab switch is instant (0ms motion).
- Principle 2 (keyboard-first) — WAI-ARIA arrow-key model; Home/End reach extremes; one Tab stop per list.
- Principle 3 (focus is a managed cursor) — roving tabindex; focus enters the selected tab and exits to its panel.
- Principle 5 (motion is semantic) — no motion on high-frequency tab switch.

(Principle numbers per SKILL.md.)

## Sources

- WAI-ARIA Authoring Practices, Tabs pattern — https://www.w3.org/WAI/ARIA/apg/patterns/tabs/
- Base UI Tabs — https://base-ui.com/react/components/tabs
- Base UI accessibility overview — https://base-ui.com/react/overview/accessibility
- `keyboard-and-focus.md § Listbox / List row` (roving tabindex reference pattern)
- `motion.md § The frequency-and-novelty matrix`, `§ Duration defaults`
- `patterns/skeleton-vs-spinner.md` (async panel loading)
- `stack-bindings/web-primitives.md § Per-component binding`
- `_decisions-ledger.md § 2026-04-21` (promotion from v1.1 deferred to v0)
