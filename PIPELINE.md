# product-design plugin — pipeline

The plugin's three skills compose with `superpowers` to walk product design from idea to shipped surface. **9 stages total; user navigates 3** (the plugin's own skills); the rest are upstream/downstream peers.

## Canonical pipeline

```
[entry — user prompt]
    │
    ↓
1. superpowers:brainstorming      ← optional pre-stage; PA Intent Q1 suggests if prompt is bare
    │
    ↓
2. /product-design:product-architecture                                            [PLUGIN SKILL 1]
    │  output: structural spec → docs/product-architecture/<date>-<slug>.md
    │  gates: HARD-GATE
    ↓
3. /product-design:visual-design                                                    [PLUGIN SKILL 2]
    │  output: visual spec → docs/visual-design/<date>-<slug>.md (incl. CSS tokens)
    │  gates: HARD-GATE
    ↓
4. /product-design:behavior-first-design                                            [PLUGIN SKILL 3]
    │  output: behavior spec → docs/behavior-first-design/<date>-<slug>.md
    │  gates: HARD-GATE
    ↓
5. superpowers:writing-plans
    │  consumes: all 3 specs + project context
    │  output: implementation plan
    ↓
6. superpowers:subagent-driven-development (uses superpowers:test-driven-development per task)
    ↓
7. superpowers:verification-before-completion
    ↓
8. superpowers:requesting-code-review (& receiving-code-review)
    ↓
9. superpowers:finishing-a-development-branch
    │
    ↓ outcome: surface implemented; every decision traces to a canon-grounded spec
```

## One-line role per skill

- **`product-architecture`** — Intent → Structure → Flows → Disclosure (4-layer guided dialogue). Produces structural spec.
- **`visual-design`** — Mood → Typography → Color → Spacing → Motion → Polish (6-layer guided dialogue). Produces visual spec with CSS tokens.
- **`behavior-first-design`** — Inputs → Focus & Keyboard → Response-time & Optimism → Feedback & Recovery → Motion fire-policy → Component binding (6-layer guided dialogue). Produces behavior spec.
- **`superpowers:brainstorming`** — refines bare prompts before product-architecture (optional pre-stage).
- **`superpowers:writing-plans`** — converts the 3 specs into an implementation plan with concrete tasks.
- **`superpowers:subagent-driven-development`** — canonical execution mode for the plan; uses TDD discipline per task.
- **`superpowers:test-driven-development`** — discipline within execution.
- **`superpowers:verification-before-completion`** — pre-merge verification.
- **`superpowers:requesting-code-review` + `receiving-code-review`** — peer review.
- **`superpowers:finishing-a-development-branch`** — close the development branch.

## Heterogeneity is deliberate

Mixing `superpowers` + `product-design` skills in one chain is the design. The product-design plugin produces durable design specs; `superpowers` produces durable code. Specs are upstream of plans; plans are upstream of code. Each skill has a single sharp Intent; the chain composes them.

The plugin works without `superpowers` installed (you can read specs and write code by hand), but the two are designed to compose. Specifically:
- `product-architecture` benefits from `superpowers:brainstorming` upstream when prompts are bare
- `behavior-first-design`'s spec output is structured for `superpowers:writing-plans` consumption

## Exit ramps

- **Skip product-architecture** if you already have a structural spec and need to enrich the visual or behavior layer.
- **Skip visual-design** if your product is non-visual (CLI tool, voice, embedded). Behavior-first-design boots cold; quality degrades but works.
- **Skip behavior-first-design** if you have a structural+visual spec and want to skip directly to plan/code (`superpowers:writing-plans` accepts both specs without behavior-first-design's contract).
- **Skip everything except brainstorming** for ideation that doesn't lead to design.

## See also

- `plugins/product-design/skills/<skill>/SKILL.md` — per-skill entrypoints
- `plugins/product-design/references/execution-skills.md` — allowlist consumed at every plugin-exit gate
- `plugins/product-design/skills/<skill>/references/_decisions-ledger.md` — design history per skill
