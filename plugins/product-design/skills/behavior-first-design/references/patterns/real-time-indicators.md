# Real-time indicators

For any mutation or connection that can fail or lag, the UI owes the user four states: `saving`, `saved`, `failed`, `offline`. Each state has a distinct visual treatment and a distinct duration contract. Show the state next to the thing it affects, not in a global toast, so that the user's gaze does not have to leave the edit to know its fate. Collapse the four states into a single on/off "sync" dot and you lose all diagnostic signal the user needs to trust the system.

## Decision table

| State | Visible? | Duration |
|---|---|---|
| saving | Yes (subtle) | Until resolved |
| saved | Yes | 2s, then fade |
| failed | Yes | Until user-acknowledged |
| offline | Persistent | Until connectivity returns |

## Rationale

Nielsen's Response Time Triad (`foundations.md § Nielsen's Response Time Triad`) sets the 1-second flow boundary: any operation taking longer than 1s must signal that it is running, or the user will either retry (double-save) or assume failure. The `saving` state is a pre-emptive answer to that question for mutations whose network round-trip can cross the budget. Making it subtle (a small dot, a softened cell border) avoids visual noise on the fast 99% of saves while still existing for the slow 1%.

`saved` fading after 2s enforces that success is the default. A persistent green checkmark next to every edited field is clutter; 2s is long enough for the user to register it and short enough that the UI returns to its neutral state before the next edit. `failed` inverts the contract: failure is not the default and MUST persist until acknowledged, because silent failures are how users lose work.

Eagle's four-channel feedback mandate (`voices/eagle.md § Actionable principles (principle 5)`) governs `offline`: connectivity loss is severe enough that it must be visible through an independent channel — typically a persistent banner or status pill — so the user does not confuse local-only edits with synced ones. "Graceful degradation" (`voices/eagle.md § Actionable principles (principle 8)`) then applies: offline does not block editing; it only annotates it.

See also `response-time-budget.md § Perceived-performance techniques` (Technique 1: optimistic UI) — indicators coexist with optimism; they describe the background reconciliation, they do not gate the user's foreground action.

## Applies to components

- `components/inline-edit.md` — per-field `saving` / `saved` / `failed` indicators; the primary invocation.
- `components/toast.md` — `failed` escalates to a toast only when the inline surface is out of view (e.g., row scrolled away).
- `components/list-row.md` — row-level indicator when a row is the unit of mutation (e.g., drag-to-reorder).
- `components/status-pill.md` — hosts the global `offline` indicator in the app chrome.
