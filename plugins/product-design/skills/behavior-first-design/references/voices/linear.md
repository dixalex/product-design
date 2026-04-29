# Voice: Linear

## Source

- Linear, "A calmer interface for a product in motion" (March 12, 2026) — https://linear.app/now/behind-the-latest-design-refresh
- Local scrape: `pine-research/.firecrawl/scratch/linear-design-refresh.md`

## Core quotes (verbatim, attributed)

> "Don't compete for attention you haven't earned."
> — Linear design refresh, 2026

> "Structure should be felt not seen."
> — Linear design refresh, 2026

> "If most people don't immediately notice what changed, that's probably a good sign… most of what makes software feel good is what you aren't likely to see."
> — Linear design refresh, 2026

## Actionable principles

1. **Rank every surface by attentional weight before styling it.** The element central to the current task (the Lead row the user is reading, the email body in Peek, the focused Command palette input) carries full weight. Anything that is orientation-only — sidebar, tab chrome, breadcrumbs, section dividers — should recede. If a chrome element is as bright as content, it has not earned its salience.

2. **Dim supporting chrome rather than removing it.** Linear did not delete the sidebar; they made it "a few notches dimmer" so the main area takes precedence. For a CRM: keep the nav sidebar, but mute icon contrast, drop colored team backgrounds, and reduce inactive-label opacity. Orientation remains available; it stops shouting.

3. **Prefer soft structure over hard separators.** Borders should imply relationships, not fence them. Round edges, soften contrast, delete dividers that don't clarify a relationship. For Lead list rows: rely on consistent row height and typographic rhythm before reaching for a border. If you can remove a separator and the structure still reads, remove it.

4. **Scale icons and chrome down, not up.** When icons become redundant or purely decorative, shrink or remove them. For a CRM row: the Lead avatar earns its slot (identity), the stage chip earns its slot (status). A decorative folder icon beside a folder labeled "Folder" has not earned its slot.

5. **Ship refreshes behind feature flags, compare side-by-side.** A component refresh should be toggleable so reviewers can A/B the old vs. new in the live app without a rebuild. This is a design methodology, not only an engineering one — it directly affects whether restraint decisions survive review.

6. **Treat information density as a constraint, not a style.** Linear's whole design spec is "preserve rich density without overwhelm." For a CRM, the List view is dense by mandate. The answer is not fewer columns — it is quieter columns, with the user's current task allowed to dominate.

## When to load this voice

Load `voices/linear.md` when any of these conditions apply:

- The user says "feel like Linear", "calmer", "less busy", "Linear-style list".
- Designing a **list row** (Lead row, inbox row, activity row) — Linear's restraint directly applies.
- Designing the **command palette** — structure, hierarchy, contrast tradeoffs.
- Making an **information hierarchy** decision: what recedes, what stays prominent.
- Any **restraint vs. polish** tradeoff where "add a border / add an icon / add a color" is on the table.
- Designing **sidebar / nav / tab bar** — the main worked-example area of the refresh.
- A reviewer complains the UI "feels busy" or "competes with itself" — this is the diagnostic voice.

See also: `foundations.md § Hick's Law` (load alongside for quantitative restraint), `motion.md § prefers-reduced-motion (reduced motion)` (Linear restraint extends to motion), `voices/rauno-mechanics.md § Actionable principles (principle 1)` (complementary restraint angle).
