# Voice: Superhuman

## Source

- Superhuman blog, "Why Superhuman Mail is built for speed: applying the 100ms rule to email" (Jun 28, 2022)
- Local scrape: `pine-research/.firecrawl/scratch/superhuman-speed.md`

## Core quotes (verbatim, attributed)

> "The 100ms rule states that every digital interaction should be faster than 100ms… 100ms is the threshold 'where interactions feel instantaneous'."
> — Paul Buchheit, Gmail creator, cited by Superhuman

> "Superhuman Mail treats the 100ms rule as the maximum, and actually aims for latency less than 50ms whenever possible."
> — Superhuman, 2022

> "First, we had the 100 ms rule. Then, the 50 ms rule. But our new renderer is so fast, it can show emails in 1-2 Chrome frames! (<32 ms)"
> — Rahul Vohra, CEO of Superhuman, 2019

> "Our approach became two-pronged: 1. Measure everything you want to make fast. 2. Make it fast."
> — Superhuman, 2022

## Actionable principles

1. **100ms ceiling, 50ms ambition, 32ms render target.** Every repeated interaction (send, archive, next-thread, save, select-lead, open-peek) must complete under 100ms end-to-end. Write the budget into the component spec. If you cannot hit 100ms, the interaction is incorrectly architected — do not paper over it with a spinner.

2. **Measure first, then optimize.** Every latency-sensitive component (list scroll, palette open, Peek render, keyboard action) needs a live measurement harness. No "feels fast" claim is credible without a number.

3. **Optimistic UI over spinners for reversible actions.** For cheap-to-roll-back actions (tag, archive, mark-as-done, assign), commit locally at 0ms and reconcile in the background. Surface a toast only on the rare failure. Spinners on actions that could be instant are a failure of nerve.

4. **Prerender, preload, cache — the next state is predictable.** Prerender the next Lead's Peek when the user hovers or arrow-keys toward it; keep the last N detail panels warm; cache view queries locally so returning to a List is instant, not a cold fetch.

5. **Keyboard shortcuts are the fast path, and the UI teaches them.** Every frequent action has a shortcut, shown in the UI (command palette, hover tooltip) so users learn without docs.

6. **Minimal animation on frequent paths.** Animations cost frames; reserve motion for rare / novel / spatial-continuity moments. (See `voices/rauno-mechanics.md § Actionable principles (principle 1)` for the complementary argument.)

## When to load this voice

Load `voices/superhuman.md` when any of these apply:

- **Any latency decision** — budget, measurement, failure mode.
- **Spinner vs. optimistic UI** — use Superhuman's bias toward optimistic commit.
- **Writing a perf budget** for a component (cited from `response-time-budget.md § Numeric targets`).
- **Keyboard shortcut design** — what earns one, how it is surfaced.
- User says **"feel fast" / "instant" / "snappy" / "like Superhuman"**.
- **Prefetch / prerender** strategy for list → detail (Lead list → Lead Peek).

See also: `foundations.md § Doherty Threshold`, `response-time-budget.md § Numeric targets`, `_decisions-ledger.md § 2026-04-17 item 7` (50ms ambition for Pine CRM hot paths).
