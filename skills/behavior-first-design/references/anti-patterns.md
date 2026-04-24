# Anti-patterns (don't do these)

> **This file is deliberate inversion.** Instead of asking *"what makes a good UI?"* (answered in the other references), we enumerate *"what guarantees a bad UI?"* Per first-principles methodology (Jacobi / Munger), inversion reveals paths to failure that success-focused thinking often overlooks. Scan your output against this list before declaring done.

The affirmative "what works" catalogue lives in [foundations.md](foundations.md) and the topic-specific reference files. The methodology behind keeping a separate inversion list is recorded in [_decisions-ledger.md § 2026-04-17](_decisions-ledger.md) item 13: treating failure paths as their own artifact forces us to look at the UI from the opposite direction, which surfaces regressions that positive checklists miss.

Use this file as a final pre-flight check, not as a substitute for the positive references. If you find an anti-pattern in your output, the fix usually lives in the companion file that owns the topic — each section below points there.

## Behavior anti-patterns

These are product-level behavior mistakes — shape-of-interaction errors that pass accessibility linters but still produce a bad UI. For deeper rationale on the first four items (autofocus, tabindex, title, aria-label), see [keyboard-and-focus.md § Anti-patterns (from Nordhealth)](keyboard-and-focus.md). The motion items cross-reference [motion.md § Duration defaults](motion.md) and [motion.md § Easing](motion.md). The latency-shaped items (spinner thresholds, confirm vs. toast-with-undo) trace back to [response-time-budget.md § Perceived-performance techniques](response-time-budget.md).

- [ ] `autofocus` attribute on any element (use imperative `focus()` when needed)
- [ ] `tabindex` values other than `0` or `-1`
- [ ] `title` attribute as tooltip (use Tooltip component)
- [ ] `aria-label` where visually-hidden text would work
- [ ] Spinner for operations expected under 200ms (use optimistic UI)
- [ ] Confirm modal for reversible operations (use toast-with-undo)
- [ ] Modal for single-field edits (use inline-edit)
- [ ] Color-only status indicators (must also have text or icon/shape)
- [ ] Motion on frequent/repeated actions (row select, keyboard nav)
- [ ] Bouncing or spring-based animations in product UI
- [ ] Animation durations >300ms
- [ ] Focus moved without user action on background content update
- [ ] Focus lost to body after content removal
- [ ] Double-click required for primary action (single-click opens, right-click for context menu)

## Accessibility anti-patterns (web essentials)

These are the floor, not the ceiling — WCAG 2.1 AA baseline issues that a design-system component should never ship. They are distinct from the behavior list above: a screen can pass every item here and still be a hostile UI, which is exactly why we keep both lists. The source checklist is Nordhealth's accessibility page (see Source), which maps cleanly onto WCAG 2.1 AA success criteria. If you catch an item here late, it usually means the semantic primitive was wrong from the start — pick a different element rather than patching ARIA on top.

- [ ] `onclick` on non-semantic element (`div`/`span`) without keyboard handler
- [ ] Form input without associated `<label>` (or `aria-labelledby`)
- [ ] Image without `alt` (or explicitly empty `alt=""` if decorative)
- [ ] Heading levels skipping (h1 → h3 skipping h2)
- [ ] Interactive element with insufficient contrast ratio (<4.5:1 text, <3:1 UI)
- [ ] No keyboard focus indicator (or removed via `outline: none` with no replacement)

## Performance anti-patterns

Performance anti-patterns are sneaky because the code looks correct in isolation and the page "feels fine" on the developer's laptop. They only surface under the conditions that matter: long lists, slow networks, mid-range devices, repeated interactions. The affirmative techniques (optimistic UI, skeletons, deferred work, virtualization thresholds) live in [response-time-budget.md § Perceived-performance techniques](response-time-budget.md). The item below about `performance.mark` is the instrumentation that lets you know whether the other anti-patterns are biting you in production — without it, you are guessing.

- [ ] Not memoizing row components in long lists
- [ ] Fetching on every render
- [ ] Blocking main thread on synchronous layout calculations
- [ ] Loading all data upfront for long lists (use virtualization >50 items)
- [ ] No `performance.mark` instrumentation on user-facing interactions

## Source
- Nordhealth accessibility checklist
- WCAG 2.1 AA
