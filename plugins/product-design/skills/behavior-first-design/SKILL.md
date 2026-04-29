---
name: behavior-first-design
description: "Behavior decisions for product surfaces — behavior spec written before component code is generated. Guided dialogue through Inputs, Focus & Keyboard, Response-time & Optimism, Feedback & Recovery, Motion fire-policy, Component binding layers. Reads structural + visual specs and produces a behavior spec (frontmatter + per-layer prose + Component-binding table) that superpowers:writing-plans consumes downstream. Use when the user says 'design the behavior,' 'keyboard model,' 'focus and motion,' 'optimistic UI,' 'component bindings,' or 'behavior contracts for components.' NOT for IA / object model (use product-architecture); NOT for visual styling (use visual-design); NOT for code generation (use superpowers:writing-plans + subagent-driven-development after the spec is written)."
---

# behavior-first-design

**Requires sibling skills `product-architecture` (upstream, structural spec) and `visual-design` (upstream, visual spec) — co-installed in the `product-design` plugin.** This skill consumes BOTH upstream specs at startup. If either is missing, the dialogue can run cold but quality degrades.

## Why this skill exists

Behavior decisions (keyboard chord maps, focus trapping, response-time budgets, optimistic UI, motion fire-policy, component bindings) need a deliberate decision phase upstream of code generation, not improvised by `superpowers:writing-plans` at execution time. Without it, the same product designed in two sessions produces two different keyboard models with no decision trail. This skill walks 6 layers as gated dialogues and terminates in a behavior spec that downstream skills consume as upstream truth.

## Layer model

```
Inputs → Focus & Keyboard → Response-time & Optimism → Feedback & Recovery → Motion fire-policy → Component binding → (behavior spec written)
```

Per-layer canon, derivation method, dialogue questions, options pattern, and gate criteria live in `references/layers/{inputs,focus-and-keyboard,response-time,feedback-and-recovery,motion-fire-policy,component-binding}.md`. Load the relevant file when entering a layer — do not inline its contents here.

**Mid-session checkpoint after Layer 3** (Response-time & Optimism): opt-in pause for fatigue. Spec in progress saves to `<project-root>/docs/behavior-first-design/<date>-<slug>.md` (incomplete); resume in new session reads marker.

## Dialogue shape

One question at a time. Lead with a recommended default ("my lean is X because…"). Skill proposes 1–3 derivation-based options at each layer (NOT peer-shapes; peer voices are cited as precedent AFTER the user picks). Per-layer transition prompts are verbatim — see Gates.

**Constraint surfacing:** at start of each layer, skill reads structural + visual specs for performance/input/visual constraints (virtualization, keyboard-primary, near-monochrome palette) and surfaces them as floors that mood-derived choices adjust within.

**User-supplied voices — runtime mechanics (inherited from visual-design verbatim):**

1. *Capture during dialogue:* at every layer's peer-comparison step, skill asks: *"Any reference beyond the canonical voices? Drop a name or URL."* Capture each as `{name, url, layer_first_cited}` into an in-session `user_voices_queue`.
2. *Defer research:* do NOT fetch during dialogue. Queue grows across layers.
3. *Resolve at spec-write time:* iterate `user_voices_queue` and write voice files to `<project-root>/docs/behavior-first-design/voices/<slug>.md` (project-local).
4. *Fallback if web access blocked:* write stub voice file with `status: "research deferred"`.
5. *Never auto-promote to plugin canon.*
6. *Frontmatter `user_voices` field* lists slugs; structurally validates at spec-write.

## Canonical references

`foundations.md` (cross-cutting laws: Hick/Miller/Jakob/Fitts/Tesler/Peak-End/Aesthetic-Usability), `keyboard-and-focus.md` (Layer 2), `motion.md` (Layer 5 fire-policy), `response-time-budget.md` (Layer 3), `anti-patterns.md` (Layer 4), `components/*.md` (Layer 6 — Common 18), `patterns/*.md` (cross-component). SKILL.md does not summarize canon — load when needed at the relevant layer.

## Handoff spec format

Markdown + YAML frontmatter + per-layer H2 sections + Component-binding table. See `references/handoff-spec-template.md` for the full template.

Spec lands at `<project-root>/docs/behavior-first-design/<YYYY-MM-DD>-<slug>.md`. The Component-binding table is *upstream truth* — `superpowers:writing-plans` consumes it for downstream task generation.

**v0.3.0 backward-compat (until v0.5.0):** Skill accepts both legacy `*_brief:` and new `*_spec:` frontmatter keys when reading upstream specs. v0.5.0 drops legacy support.

**Multi-spec disambiguation:** if `<project-root>/docs/product-architecture/` OR `<project-root>/docs/visual-design/` contains multiple spec files, skill loads the most-recent by `date:` frontmatter (not file mtime, not filename); ties broken by filename slug ascending. Surfaces once at boot per upstream skill found. User can override with explicit path. Default proceeds without re-prompt.

**On `yes` at the final-layer transition (Component binding → spec-write):**
1. Skill writes the behavior spec file to `docs/behavior-first-design/<date>-<slug>.md` immediately.
2. Skill prints a one-line confirmation: `Wrote spec to <path> — review the file before invoking downstream skills.`
3. Skill emits the Execution Suggestions block (per `plugins/product-design/references/execution-skills.md`).
4. Control returns to user. The skill DOES NOT auto-invoke a downstream skill or auto-open the file in an editor.

**On `revise`:** re-enter the most-recent layer's dialogue from Q1; user can re-walk that layer or use a layer keyword to jump (`inputs`, `focus`, `response-time`, `feedback`, `motion`, `binding`).

## Domain awareness

Inherited from structural + visual specs. Reads `domain` and `domain_peers` from upstream frontmatter; uses both to seed Component-binding candidates and to scope which canonical voices fire (e.g., dev-tool domain → Linear voice prominent for keyboard chords; commerce domain → Shopify Polaris voice prominent for feedback patterns).

## Gates and discipline

<HARD-GATE>
Do NOT invoke `superpowers:writing-plans`, `subagent-driven-development`, `frontend-design`, or any execution skill, write any code, scaffold any project, or take any implementation action until the behavior spec is written to disk AND the user has explicitly approved it. This applies to EVERY project regardless of perceived simplicity. The spec is the gate. If the user pushes to skip ahead, remind them that the spec exists so downstream code has behavior contracts to compose against — without it, every regen produces a different keyboard model and focus discipline drifts session-to-session.
</HARD-GATE>

Verbatim transition prompts — emit word-for-word at each layer boundary, no paraphrasing:

- Layer 1→2: `"Inputs captured. (Layer 1 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to move to Focus & Keyboard, or should I revise? (yes / revise)"`
- Layer 2→3: `"Focus & Keyboard captured. (Layer 2 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to move to Response-time & Optimism, or should I revise? (yes / revise)"`
- Layer 3→4: `"Response-time & Optimism captured. (Layer 3 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to move to Feedback & Recovery, or should I revise? (yes / revise)"` (also: mid-session checkpoint prompt fires here)
- Layer 4→5: `"Feedback & Recovery captured. (Layer 4 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to move to Motion fire-policy, or should I revise? (yes / revise)"`
- Layer 5→6: `"Motion fire-policy captured. (Layer 5 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to move to Component binding, or should I revise? (yes / revise)"`
- Layer 6→spec-write: `"Component binding captured. (Layer 6 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.) Ready to write the behavior spec, or should I revise? (yes / revise)"`

## When NOT to invoke

- IA / surface decisions / object model ("design a CRM") → `product-architecture`
- Visual styling / typography / color / spacing / motion VALUES → `visual-design`
- Code generation / scaffolding / component implementation → `superpowers:writing-plans` + `subagent-driven-development` AFTER the spec is written
- Frontend execution polish (visual surfaces) → `frontend-design` (Anthropic official) AFTER the spec is written
- Print / email / out-of-product surfaces — out of scope (web product surfaces only at v1)

## Related skills

- `product-architecture` (sibling, upstream) — produces structural spec that this skill reads for surfaces, JTBD, persona, Flows
- `visual-design` (sibling, upstream) — produces visual spec with CSS tokens that this skill references in motion fire-policy + emitted contracts
- `superpowers:writing-plans` (downstream) — consumes the behavior spec; produces an implementation plan with concrete tasks per surface-action binding
- `superpowers:subagent-driven-development` (downstream execution) — executes the writing-plans output via TDD discipline
- `frontend-design` (Anthropic official, downstream execution) — visual-surface polish; reads visual spec as upstream truth, behavior spec as explicit context
- See `PIPELINE.md` at plugin root for the full 9-stage canonical pipeline and stage-by-stage role lines.
