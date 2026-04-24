# Skeleton vs spinner

Pick the loading indicator by expected duration, not by aesthetic preference. Under 200ms, show nothing — the content arrives before the user's eye locks. Between 200ms and 1s, render a skeleton that matches the final layout so the page does not reflow on arrival. Between 1s and 3s, keep the skeleton but add a progress sense (shimmer, bar, counter). Over 3s, replace the skeleton with a spinner plus an estimated time and a cancel control, because the interaction has left "loading" territory and entered "long-running job" territory.

## Decision table

| Expected duration | Indicator |
|---|---|
| <200ms | Nothing — instant swap |
| 200ms–1s | Skeleton (matches layout) |
| 1s–3s | Skeleton + progress sense |
| >3s | Spinner + estimated time + cancel |

## Rationale

`response-time-budget.md § Perceived-performance techniques` (Technique 2) is the canonical rule: skeletons are only for >200ms fetches. A skeleton that flashes for 80ms reads as a bug — the user's eye registers a shape change without ever parsing the skeleton as "loading." Gate skeletons behind a `setTimeout(200)` that is cleared when data arrives.

The Doherty Threshold (`foundations.md § Doherty Threshold`) justifies the 1-second line: under 1s, users stay in flow and a static skeleton is enough. Past 1s, the brain wants feedback that something is progressing — a shimmer or a counter. Past 3s (outside Nielsen's flow budget, `foundations.md § Nielsen's Response Time Triad`), the user has disengaged from the interaction and needs explicit agency: a cancel button and a time estimate convert a broken-feeling wait into a deliberate operation.

The >3s case is rare by design. Superhuman's third principle (`voices/superhuman.md § Actionable principles (principle 3)`) argues that optimistic UI beats spinners for reversible actions: if you are drawing a spinner, first ask whether the mutation could commit locally at 0ms and reconcile in the background. Most spinners in CRM products are architectural failures — not indicator choices.

See also `foundations.md § Jakob's Law` on why the skeleton shape should match the final layout: users have learned from Linear, Gmail, and Notion that skeletons are placeholder-shaped; an abstract "loading" graphic trains them to expect reflow.

## Applies to components

- `components/list-row.md` (composing `components/lead-list.md`) — list skeletons are the most frequent invocation; match row height and column count exactly.
- `components/dialog.md` — lead-detail-peek and similar detail dialogs use layout skeletons for the 200ms–1s case.
- `components/command-palette.md` — search results use the <200ms (nothing) → skeleton rule; results that lag become a spinner only at >3s.
- `components/empty-state.md` — only renders *after* the loading indicator resolves to zero results; never overlaps a skeleton.
