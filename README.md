# product-design

A Claude Code plugin bundling three sibling skills that walk product design top-down across Garrett's Elements of UX. Each skill is a guided dialogue that produces a **spec** — a durable, auditable design artifact that downstream skills consume as upstream truth.

**Current version: v0.4.0** (shipped 2026-04-29). [See What's new](#whats-new-in-v040).

---

## Skills included

| Skill | Invocation | Layer (Garrett) | Layers in dialogue | Output |
|---|---|---|---|---|
| `product-architecture` | `/product-design:product-architecture` | Strategy / Scope / Structure | Intent → Structure → Flows → Disclosure (4) | Structural spec |
| `visual-design` | `/product-design:visual-design` | Surface | Mood → Typography → Color → Spacing → Motion → Polish (6) | Visual spec + CSS tokens |
| `behavior-first-design` | `/product-design:behavior-first-design` | Skeleton | Inputs → Focus & Keyboard → Response-time → Feedback → Motion fire-policy → Component binding (6) | Behavior spec + Component-binding table |

All three are process skills with **HARD-GATE** discipline: no downstream skill is invoked until the upstream spec is written and explicitly approved.

---

## Pipeline

The plugin's three skills compose with `superpowers` to walk product design from idea to shipped surface — **9 stages total; user navigates 3** (the plugin skills). The rest are upstream/downstream peers.

```
[user prompt]
   ↓
1. superpowers:brainstorming           ← optional pre-stage; PA Intent Q1 suggests if prompt is bare
   ↓
2. /product-design:product-architecture                      [PLUGIN] → structural spec
   ↓
3. /product-design:visual-design                             [PLUGIN] → visual spec + CSS tokens
   ↓
4. /product-design:behavior-first-design                     [PLUGIN] → behavior spec + binding table
   ↓
5. superpowers:writing-plans                                 → implementation plan from the 3 specs
   ↓
6. superpowers:subagent-driven-development (uses test-driven-development per task)
   ↓
7. superpowers:verification-before-completion
   ↓
8. superpowers:requesting-code-review (& receiving-code-review)
   ↓
9. superpowers:finishing-a-development-branch                → surface implemented
```

See `PIPELINE.md` at plugin root for the canonical diagram + role lines + exit ramps. The plugin works without `superpowers` installed (you can read specs and write code by hand), but the two are designed to compose.

---

## Install

```
/plugin marketplace add dixalex/product-design
/plugin install product-design@product-design-plugins
```

Restart the Claude Code session. `/plugin list` should show `product-design v0.4.0`; the three skills become invocable as `/product-design:<skill-name>`.

**Updating later:**

```
/plugin marketplace update product-design-plugins
```

Then `/plugin` (interactive) → find `product-design` → **Update** or **Reinstall**. Restart the session.

---

## Getting started — end-to-end run

```
1. "Design a lead-tracking CRM for solo founders. Use the product-architecture skill."
   → produces docs/product-architecture/<date>-<slug>.md (structural spec)

2. "Now run visual-design to write the visual spec."
   → produces docs/visual-design/<date>-<slug>.md (visual spec + CSS tokens)

3. "Now run behavior-first-design to write the behavior spec."
   → produces docs/behavior-first-design/<date>-<slug>.md (behavior spec + Component-binding table)

4. "Use superpowers:writing-plans to turn the 3 specs into an implementation plan."
   → produces a per-task plan with concrete file paths and code

5. "Execute via superpowers:subagent-driven-development."
   → scaffolds React / Next.js / etc. components into your project; tokens come from var(--*) refs in the visual spec
```

Each plugin skill gates on user approval before handing off. Specs are committed to the project repo as design records — they're auditable, re-runnable, and re-revisable.

---

## What's new in v0.4.0

- **`behavior-first-design` reshapes from code-emitter to spec-emitter.** Walks 6 layers (Inputs → Focus & Keyboard → Response-time → Feedback → Motion fire-policy → Component binding) as a guided dialogue. Output is a behavior spec consumed by `superpowers:writing-plans` downstream — not React component code. **In-flight v0.3.0 users:** running step 4 of the pipeline now produces a spec, not code; downstream consumption switches to `superpowers:writing-plans`.
- **`brief` renames to `spec`** everywhere. Frontmatter keys (`visual_brief:` → `visual_spec:`) accept both legacy and new forms through v0.5.0 (one-version grace).
- **Plugin-root `PIPELINE.md`** with canonical 9-stage diagram. Every gate prompt now includes a "you-are-here" position prefix (e.g., "Layer 2 of 6 in visual-design; skill 2 of 3 in product-design pipeline").
- **`plugins/product-design/references/execution-skills.md` allowlist** + Execution Suggestions block at every plugin-exit gate. Curated list of `superpowers` + `frontend-design` skills with quality grading (`always` / `proven` / `candidates_unverified`); auto-demote after 6 months without re-verification.
- **Brainstorming-exit suggestion at PA Intent Q1** when prompt is bare. Surfaces missing JTBD / persona / scope signals; user picks `brainstorming` (exit) or `proceed`.
- **Pulled in from v0.4.1**: `## Glossary` section in structural spec template (Pocock Ubiquitous Language); specs-to-code anti-pattern entry in visual-design's `anti-patterns.md`.
- **UX gaps closed:** multi-spec disambiguation, spec-review approval mechanism, mid-pipeline upstream-revision contract.

**Plugin pivot:** v0.4.0 reframes the plugin from "design + scaffold tool" to *"three sibling skills that produce specs that compose with `superpowers`."* Operating principle (Beck): *"Invest in the design of the system every day."* Specs are the daily-design artifact for AI-built surfaces.

---

## Earlier releases

### v0.3.0
- `visual-design` ships as a process skill (6 layers; Mood → Polish).
- Pipeline order inverts: visual-design now runs BEFORE behavior-first-design.
- Tokens migrate from `behavior-first-design/references/tokens/` → `visual-design/references/tokens/`. Motion duration literals (150ms / 180ms / 200ms / 0ms) extracted into `motion-defaults.md`.
- User-supplied voices land project-local (never auto-promoted to plugin canon).

### v0.2.1
- Structure-layer object-derivation bug fix in `product-architecture` (peer-mimicry → first-principles via verb-back-derivation).

### v0.2.0
- `product-architecture` ships.

### v0.1.0
- `behavior-first-design` ships (originally as code-emitter; reshaped to spec-emitter at v0.4.0).

---

## Canonical references

Each skill cites its sources inline. Cross-cutting:

- Garrett, *The Elements of User Experience* (5-plane model)
- Sophia Prater, *Object-Oriented UX*
- Daniel Jackson, *The Essence of Software*
- Adam Wathan + Steve Schoger, *Refactoring UI*
- Dieter Rams, *Ten Principles for Good Design*
- Ellen Lupton, *Thinking with Type*
- Apple Human Interface Guidelines — Foundations, Inputs, Motion
- Nielsen, *10 Usability Heuristics* (1994); *Response Times: 3 Important Limits* (1993)
- Doherty Threshold (1982)
- Jon Yablonski, *Laws of UX*
- Christensen, *Jobs to be Done*
- Pocock, *Software fundamentals matter now more than ever* (2026)

**Canonical voices (visual-design):** Linear, Stripe, Operate, Rauno Freiberg's craft essays.

---

## For plugin authors adding executable tooling

When adding scripts, agents, or hooks in a future version, use `${CLAUDE_PLUGIN_ROOT}` to reference intra-plugin paths portably; `${CLAUDE_PLUGIN_DATA}` for writable state. v1 is markdown-only — neither variable is exercised yet.

---

## License

MIT. Use freely. Attribute when you can.

## Contributing

Issues and PRs welcome at [github.com/dixalex/product-design](https://github.com/dixalex/product-design). Each skill's `_decisions-ledger.md` records prior design decisions — consult before proposing changes.
