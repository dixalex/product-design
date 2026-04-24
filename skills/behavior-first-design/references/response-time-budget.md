# Response-time budget

Every user-initiated UI update must hit its budget or is a bug. Speed is not a polish item — it is the substrate of perceived quality. Nielsen's 100ms limit and Superhuman's 50ms ambition are not "nice to haves"; below these thresholds, users feel they are manipulating objects directly. Above them, they feel they are waiting on a computer. This file translates those thresholds into concrete budgets a CRM UI must meet, and the techniques used to stay inside them.

Pair this file with `foundations.md § Nielsen Response Time Triad` and `foundations.md § Doherty Threshold` for the underlying behavioral laws.

## Numeric targets

Read the table as: every interaction below must meet its P95 target on target hardware (current-gen MacBook, Chrome/Safari, wired connection). The "outer bound" is the absolute failure point — anything past it is a bug ticket, not a performance tuning task. P95 is measured from the user's input event to the next committed paint, measured via `performance.mark` bracketing.

| Interaction | Target (P95) | Outer bound | Source |
|---|---|---|---|
| input → paint (click feedback) | 16ms | 100ms | Nielsen 1993, Superhuman 50ms ambition |
| click → local state change | 50ms | 100ms | Superhuman |
| ⌘K → palette visible | 50ms | 100ms | Superhuman |
| inline-edit open | 50ms | 100ms | Linear/Superhuman |
| dialog open | 100ms | 400ms | Doherty |
| row hover reveal | 16ms | 50ms | Nielsen instant |
| optimistic mutation apply (local) | 50ms | 100ms | Linear changelog |
| any server reconciliation | (optimistic; rollback path) | 2s | — |

Note on the last row: server reconciliation has no user-facing latency budget because it is never on the user's critical path. The UI commits locally first; the server call runs in the background, and a failure triggers a rollback + undo toast. If a mutation cannot be designed this way, reconsider the mutation shape before accepting a longer budget.

Note on Row 1: 16ms is the per-frame paint budget at 60fps; 100ms is the Nielsen perceptual "feels instant" threshold. A paint that lands between them is a regression to triage, not an outage.

## Perceived-performance techniques

The budgets above are unreachable on every interaction by brute force. These six techniques are how real apps actually stay in the green.

1. **Optimistic UI** — apply the mutation locally, reconcile with the server after. On server error, roll back the local state and surface an undo toast with the failure reason. This pattern is load-bearing: without it, any mutation with >50ms server RTT is already out of budget. See `patterns/optimistic-ui.md` for the full pattern.
2. **Skeleton screens only for >200ms** — if the fetch or mutation is expected to return in under 200ms, render nothing transient. A skeleton that flashes for 80ms is worse than no skeleton; it reads as a bug. Gate skeletons behind a `setTimeout(200)` that's cleared when data arrives.
3. **Prefetch on hover** — for click targets that will load data (opening a lead, viewing a contact), fire the fetch on `mouseenter` or keyboard focus, not on click. Humans take ~100–200ms between hover and click; that's a free budget.
4. **Aggressive memoization** — list-row renders should be `React.memo` (or framework equivalent) with equality by stable `id`. A 500-row list that rerenders every row on every state change will blow the 16ms paint budget on any laptop.
5. **Interruptible transitions** — if the user clicks mid-animation (dialog opening, panel sliding), cancel the animation and respond to the new input immediately. A non-interruptible 250ms animation is a 250ms input latency bug. Respect `prefers-reduced-motion` unconditionally (see `motion.md § reduced motion`).
6. **Don't measure what you don't intend to keep under budget** — every `performance.mark` that ships to production is an implicit CI assertion. Add marks for interactions you are committed to defending; delete marks for interactions you are not. A dashboard of metrics no one owns is noise.

## Sources

- Nielsen, J. "Response Times: The 3 Important Limits" (1993). https://www.nngroup.com/articles/response-times-3-important-limits/ — the canonical 0.1s / 1.0s / 10s thresholds, still current.
- Doherty, W. J. & Thadani, A. J. "The Economic Value of Rapid Response Time" (IBM 1982) — establishes that sub-400ms response pays for itself in user throughput.
- Superhuman / Marfice, C. "Why Superhuman Mail is built for speed: applying the 100ms rule to email" (2022). https://blog.superhuman.com/superhuman-is-built-for-speed/ — origin of the 50ms ambition and the <32ms render goal.
- Linear changelog, "Improving performance at scale" (2021) — optimistic-UI and memoization patterns applied in a production CRM-adjacent app.
