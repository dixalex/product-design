# Multi-select

Match Linear and Gmail exactly. A single click opens — it does not toggle selection. Selection is a separate intent expressed by Cmd/Ctrl-click (individual) or Shift-click (range). Space toggles the peek for the focused row. Clicking outside the list clears selection. Cmd/Ctrl+A selects all visible. Deviating from this mapping is the single fastest way to make a list feel wrong.

## Decision table

| Action | Effect | Source |
|---|---|---|
| Click row | Open peek / focus row | Linear |
| Space | Toggle peek | Linear |
| Cmd/Ctrl+click row | Toggle row in selection | Linear/Gmail |
| Shift+click row | Extend selection from anchor to here | Linear/Gmail |
| Click outside list | Clear selection | Linear |
| Cmd/Ctrl+A | Select all visible | Standard |

## Rationale

Jakob's Law (`foundations.md § Jakob's Law`) is the controlling principle. Users spend thousands of hours per year in Gmail and increasingly in Linear; those interactions have hardened into motor memory. Any list whose click behavior differs from those two products pays an immediate recognition tax — the user has to slow down to check that a click didn't select something, or that a Cmd-click didn't open something. That tax is unrecoverable on frequent paths.

Separating "open" from "select" (single-click opens, Cmd/Ctrl-click toggles selection) is the decision that lets peek-on-click work alongside bulk operations. If a single click toggled selection, every navigation would risk losing the current selection set — which is exactly what makes old checkbox-grid UIs feel tedious. Peek on click + explicit-modifier selection gives the user a keyboard-free scan mode and a keyboard-mediated batch-action mode on the same list, without ever forcing a toggle.

Clicking outside the list clearing selection mirrors desktop file-manager behavior and prevents the common "why are these still selected?" trap after the user has moved on. Cmd/Ctrl+A selecting visible rows (not the full virtual list) keeps the action predictable — selecting 50,000 off-screen rows by accident is a destructive-undo scenario we refuse to design for.

See `_decisions-ledger.md § 2026-04-17` (item 4) for the project ruling. Eagle's multi-select mandate (`voices/eagle.md § Actionable principles (principle 3)`) reinforces the pattern: "Shift-range, Cmd-individual, Cmd-A all — everywhere a list appears."

**Focus-cursor invariant.** Click and keyboard share one focus index. `mousedown` on a row moves the roving index to that row; j/k/arrow keys continue from wherever the last click landed. If a click lands on a row's action button (e.g. Archive), the listbox re-receives focus afterward so j/k don't fall through to the body. This is the load-bearing piece that makes hybrid keyboard/mouse UX feel coherent — skipping it creates "two cursors" UX where neither input trusts the other.

## Applies to components

- `components/list-row.md` — the row itself is the interaction target; it owns the click/Cmd-click/Shift-click handlers and the anchor state for range selection.
- `components/lead-list.md` (composes `list-row`) — lead-list is the reference implementation. Any list in the product that grows past ~5 rows MUST adopt this pattern rather than inventing a toggle mode.
- `components/bulk-action-bar.md` — appears when selection count ≥ 1; actions compose over the selection set. See also `voices/eagle.md § Actionable principles (principle 3)` on batch-mode toggles being forbidden.
- `components/filter.md` — selected-row set is an input to batch filter operations.
