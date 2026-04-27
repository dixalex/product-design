# product-design

A Claude Code plugin bundling three sibling skills that walk product design top-down across Garrett's Elements of UX, from intent to surface.

**Current version: v0.3.0** — visual-design skill ships; pipeline reorders so visual decisions land before component behavior. See [What's new](#whats-new-in-v030).

## Skills included

| Skill | Invocation | Layer (Garrett) | Shape | Version |
|---|---|---|---|---|
| `product-architecture` | `/product-design:product-architecture` | Strategy / Scope / Structure (Intent → Structure → Flows → Disclosure) | Process — guided dialogue | v1 |
| `visual-design` | `/product-design:visual-design` | Surface (Mood → Typography → Color → Spacing → Motion → Polish) | Process — guided dialogue | v1 |
| `behavior-first-design` | `/product-design:behavior-first-design` | Skeleton (interaction, keyboard, focus, motion fire-policy) | Reference library + 6-step output contract | v1 |

The three skills compose: **product-architecture → visual-design → behavior-first-design**. Product-architecture produces a structural brief (objects, flows, disclosure rules); visual-design reads it and produces a visual brief (prose decisions + CSS tokens); behavior-first-design consumes both at component-emit time. Anthropic's `frontend-design` skill (separate plugin) handles execution-time polish; visual-design's tokens are upstream truth.

## Install

```
/plugin marketplace add dixalex/product-design
/plugin install product-design@product-design-plugins
```

Restart the Claude Code session. `/plugin list` should show product-design v0.3.0; the three skills become invocable as `/product-design:<skill-name>`.

To update later:

```
/plugin marketplace update product-design-plugins
```

Then `/plugin` (interactive) → find product-design → Update or Reinstall. Restart the session.

## Getting started — a four-skill run

```
1. "Design a lead-tracking CRM for solo founders. Use the product-architecture skill."
   → produces docs/product-architecture/<date>-<slug>.md (structural brief)

2. "Now run visual-design to write the visual brief. Read the structural brief at <path>."
   → produces docs/visual-design/<date>-<slug>.md (visual brief + CSS tokens)

3. "Scaffold the components per both briefs into examples/<project>/. Use behavior-first-design."
   → emits Next.js / React components that reference var(--color-bg) etc. from the visual brief

4. (Optional) "Apply visual styling per the visual brief. Use frontend-design with the visual brief as explicit context."
   → execution-time polish; visual brief is upstream truth, frontend-design implementation is best-effort
```

Each skill gates on user approval before handing off. Briefs are committed to the project repo as design records.

## What's new in v0.3.0

- **`visual-design` ships as a process skill** (Mood → Typography → Color → Spacing → Motion → Polish; guided dialogue with verbatim transition prompts; HARD-GATE on downstream skills until brief is approved).
- **Pipeline order inverts:** visual-design now runs BEFORE behavior-first-design (was: implicit, after). Visual decisions ground component implementation rather than chase it.
- **Tokens migrated** from `behavior-first-design/references/tokens/` → `visual-design/references/tokens/`. Motion duration literals (150ms / 180ms / 200ms / 0ms) extracted into `motion-defaults.md`; behavior-first-design keeps the frequency-novelty fire-policy.
- **User-supplied voices** can be added during the visual-design dialogue (queued for research at brief-write time; voice files land project-local at `<project-root>/docs/visual-design/voices/<slug>.md`; never auto-promoted to plugin canon).
- **Plugin description reordered** to reflect new pipeline: IA → Surface → Skeleton.

Earlier releases:
- v0.2.1 — Structure-layer object-derivation bug fix in product-architecture (peer-mimicry → first-principles via verb-back-derivation)
- v0.2.0 — `product-architecture` ships
- v0.1.0 — `behavior-first-design` ships

## Canonical references

Each skill cites its sources inline. Cross-cutting:

- Garrett, *The Elements of User Experience* (5-plane model)
- Sophia Prater, *Object-Oriented UX*
- Daniel Jackson, *The Essence of Software*
- Adam Wathan + Steve Schoger, *Refactoring UI*
- Dieter Rams, "Ten Principles for Good Design"
- Ellen Lupton, *Thinking with Type*
- Apple Human Interface Guidelines — Foundations
- Jon Yablonski, *Laws of UX*
- Christensen, Jobs to be Done

**Canonical voices (visual-design):** Linear, Stripe, Operate, Rauno Freiberg's craft essays.

## For plugin authors adding executable tooling

When adding scripts, agents, or hooks in a future version, use `${CLAUDE_PLUGIN_ROOT}` to reference intra-plugin paths portably; `${CLAUDE_PLUGIN_DATA}` for writable state. v1 is markdown-only — neither variable is exercised yet.

## License

MIT. Use freely. Attribute when you can.

## Contributing

Issues and PRs welcome at [github.com/dixalex/product-design](https://github.com/dixalex/product-design). Each skill's `_decisions-ledger.md` records prior design decisions — consult before proposing changes.
