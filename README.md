# product-design

A Claude Code plugin bundling three sibling skills that walk product design top-down across Garrett's Elements of UX, from intent to surface.

**Current version: v0.4.0** — `behavior-first-design` reshapes from code-emitter to spec-emitter; `brief` renames to `spec`; PIPELINE.md + execution-skills allowlist + brainstorming-exit suggestion. See [What's new](#whats-new-in-v040). **In-flight v0.3.0 users:** running step 4 of the pipeline now produces a spec (not React code); downstream consumption switches to `superpowers:writing-plans`.

## Skills included

| Skill | Invocation | Layer (Garrett) | Shape | Version |
|---|---|---|---|---|
| `product-architecture` | `/product-design:product-architecture` | Strategy / Scope / Structure (Intent → Structure → Flows → Disclosure) | Process — guided dialogue → structural spec | v1 |
| `visual-design` | `/product-design:visual-design` | Surface (Mood → Typography → Color → Spacing → Motion → Polish) | Process — guided dialogue → visual spec | v1 |
| `behavior-first-design` | `/product-design:behavior-first-design` | Skeleton (Inputs → Focus & Keyboard → Response-time → Feedback → Motion fire-policy → Component binding) | Process — guided dialogue → behavior spec | v1 |

The three skills compose: **product-architecture → visual-design → behavior-first-design → `superpowers:writing-plans` → execution.** Each skill produces a *spec* that downstream skills consume as upstream truth. Specs are markdown + YAML + (where relevant) CSS tokens — committed to the project repo as design records. See `PIPELINE.md` at plugin root for the canonical 9-stage diagram.

## Install

```
/plugin marketplace add dixalex/product-design
/plugin install product-design@product-design-plugins
```

Restart the Claude Code session. `/plugin list` should show product-design v0.4.0; the three skills become invocable as `/product-design:<skill-name>`.

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

## What's new in v0.4.0

- **`behavior-first-design` reshapes from code-emitter to spec-emitter.** Walks 6 layers (Inputs → Focus & Keyboard → Response-time & Optimism → Feedback & Recovery → Motion fire-policy → Component binding) as a guided dialogue. Output is a behavior spec consumed by `superpowers:writing-plans` downstream — not React component code. **In-flight v0.3.0 users:** running step 4 of the pipeline now produces a spec, not code; downstream consumption switches to `superpowers:writing-plans`.
- **`brief` renames to `spec`** everywhere. Frontmatter keys (`visual_brief:` → `visual_spec:`) accept both legacy and new forms through v0.5.0 (one-version grace).
- **Plugin-root `PIPELINE.md`** with canonical 9-stage diagram (3 own skills + 6 superpowers stages). Every gate prompt now includes "you-are-here" position prefix.
- **`references/execution-skills.md` allowlist + Execution Suggestions block** at every plugin-exit gate. Curated list of `superpowers` + `frontend-design` skills with quality grading (`always` / `proven` / `candidates_unverified`).
- **Brainstorming-exit suggestion at PA Intent Q1** when prompt is bare. Surfaces missing JTBD/persona/scope signals; user picks `brainstorming` (exit) or `proceed`.
- **Pulled in from v0.4.1**: `## Glossary` section in structural spec template (Pocock Ubiquitous Language); specs-to-code anti-pattern entry in visual-design's `anti-patterns.md`.
- **Multi-spec disambiguation, spec-review approval mechanism, mid-pipeline upstream-revision contract** — UX gaps closed.

**Plugin pivot:** v0.4.0 reframes the plugin from "design + scaffold tool" to "three sibling skills that produce specs that compose with `superpowers`." Operating principle (Beck): *"Invest in the design of the system every day."* Specs are the daily-design artifact for AI-built surfaces.

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
