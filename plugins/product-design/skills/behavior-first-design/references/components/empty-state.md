# Empty State

The view a user sees when a list, dashboard, or screen has no content — whether because the user hasn't created anything yet (first-run), a filter returned zero results, permission denied the data, or a request errored. Empty states are the **first impression** of a feature per `foundations.md § Peak-End Rule`; done well they invite action, done poorly they read as apology.

**Base UI has no EmptyState primitive.** Per `_decisions-ledger.md § 2026-04-17` (item 1), EmptyState is one of the six gaps. Hand-roll the layout; use `components/button.md` for the primary action. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: no
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
keyboard_activation: Enter
esc_closes: n-a
validation_timing: n-a
primary_action_count: 1
secondary_action_count_max: 1
copy_names_the_entity: yes
copy_mentions_next_step: yes
illustration_optional: yes
hero_blob_forbidden: yes
focus_lands_on_primary_action_on_mount: conditional
variants: first-run | zero-filter-results | permission-denied | error-with-retry
```

## Keyboard contract

| Key | When | Effect |
| --- | --- | --- |
| `Tab` | Empty state visible | Focus moves through: primary action → (optional secondary) → any links |
| `Enter` / `Space` | Primary action focused | Trigger the primary action (per `components/button.md`) |

If the empty state is the entire page body (first-run), **do not** auto-focus the primary action on mount — it steals focus from the navigation / breadcrumbs the user was still reading. Auto-focus only when the empty state replaces a previously-interactive region the user was actively working in (e.g., after they clear all filters and the list becomes empty).

## Behavior invariants

- **Single primary action.** Per `foundations.md § Hick's Law`, two co-equal CTAs double decision time and halve click-through. Pick one; make the rest links.
- **Copy names the entity + cause + next step.** Not "No items" — "You haven't added any leads yet. Import a CSV or create your first lead." Three required elements: entity, cause, next step.
- **Illustration optional, never a hero blob.** 64–96px icon or spot illustration is fine. Full-width abstract blobs burn the first impression on decoration (`voices/eagle.md § Actionable principles (principle 7)` — invite action, not apology).
- **Next step is one click.** The primary button *is* the next step — don't open a menu or wizard homepage; start the action.
- **Four variants, not one template:**
  1. **First-run** — inviting, action-forward.
  2. **Zero filter results** — "No leads match these filters" + "Clear all" primary action (`components/filter.md`).
  3. **Permission denied** — "This view is restricted" + "Request access" link. Don't blame the user.
  4. **Error with retry** — "Couldn't load leads" + "Retry" primary action. Cross-ref `patterns/error-surface.md`.
- **No motion beyond a 150ms fade on mount** (`motion.md § The frequency-and-novelty matrix`).
- **Help text ≤1 short paragraph.** If it needs three, the screen's job is broken; fix information architecture.

## State transitions

```
mounted (empty-state variant selected based on cause: first-run | zero-filters | permission | error)
  ↓ render icon (optional) + heading + paragraph + primary action (+ optional secondary link)
  ↓ 150ms fade-in (instant if prefers-reduced-motion)
idle (empty state visible; primary action focusable but not auto-focused in first-run variant)
  ↓ user clicks primary action
exiting (empty state unmounts; new flow begins — create form, filter cleared, retry in flight)
```

For the zero-filter-results variant, the empty state mounts *inside* the list container while the filter bar stays interactive above it — the user can adjust filters without losing the "no match" context (`components/filter.md`).

For the error-with-retry variant, retry puts the state into a loading skeleton (not this component's problem — cross-ref `patterns/error-surface.md`). On retry success, the empty state unmounts; on second failure, the same empty state re-renders with a persistent error surface below.

## Stack binding (Base UI)

**Base UI has no EmptyState primitive.** Hand-roll:

```
<div role="region" aria-label="Empty state">
  <Icon />                        // optional, 64–96px, Lucide
  <h2>Heading naming the entity</h2>
  <p>One paragraph of context + next step</p>
  <Button variant="primary">Primary action</Button>
  <a href="...">Optional secondary link</a>
</div>
```

Use `components/button.md` (Base UI `<Button>`) for the primary CTA. Wrap in a `role="region"` with an `aria-label` so screen readers announce "Empty state region" and the user can jump to it with the rotor. For the zero-filter-results variant, wire the primary action to the filter's clear-all handler in `components/filter.md`. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- **"No items" as the heading** — name the entity (`voices/eagle.md § Actionable principles (principle 7)`).
- **Two equal primary buttons** — Hick's Law violation; pick one (`foundations.md § Hick's Law`).
- **Hero illustration disconnected from action** — burns the first impression on decoration (`anti-patterns.md § Accessibility anti-patterns`).
- **Empty state instead of a loading skeleton** — render empty state only *after* the query resolves to nothing.
- **Apologetic copy** — "Sorry, we couldn't find any leads." Users aren't owed an apology; they're owed a next step.
- **Zero-filter-results with no clear-all** — user is stuck.
- **Permission-denied that blames the user** — reframe to "This view is restricted. Ask an admin."
- **Error state without retry** — always offer a retry (`patterns/error-surface.md`).

## Principles invoked

- Principle 1 (speed of interaction) — primary action goes directly to the next step, no menu in between.
- Principle 2 (obvious affordances) — the single primary action is visually the loudest element; everything else recedes.
- Principle 4 (progressive disclosure) — secondary options are links under the button, not competing CTAs.
- Principle 5 (reversibility) — filter empty state offers "Clear all" to restore; error state offers "Retry".
- Principle 7 (respect the user's cursor) — no auto-focus in first-run variant; only auto-focus when the user was actively interacting with the region.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- Eagle product principles (full file: `pine-research/design/eagle-product-principles.md`), § 21 empty-state guidance
- Linear empty-state screenshots / convention — https://linear.app/method
- Nielsen Norman Group, "Empty State UX" — https://www.nngroup.com/articles/empty-state-interaction-design/
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI EmptyState gap)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Hick's Law`, `§ Peak-End Rule`, `§ Jakob's Law`
- `voices/eagle.md § Actionable principles (principle 7)`
- `patterns/error-surface.md`
- `motion.md § The frequency-and-novelty matrix`, `§ prefers-reduced-motion`
- `anti-patterns.md § Accessibility anti-patterns`, `§ Behavior anti-patterns`
- `components/button.md`, `components/filter.md`
