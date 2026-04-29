---
name: product-architecture
description: Guided dialogue for designing a product's information architecture top-down — Intent, Structure, Flows, Disclosure. Produces a handoff spec. Use when the user says "design a CRM / app / dashboard", "information architecture", "product architecture", "structure this product", "redesign", "rearchitect", or "what screens should my product have". NOT for single-component scaffolding (use behavior-first-design).
---

# product-architecture

**Requires sibling skill `behavior-first-design`** (co-installed in the `product-design` plugin). Cross-cutting laws (Hick, Miller, Jakob, Tesler, Fitts, Peak-End) are cross-referenced from `behavior-first-design/references/foundations.md` and MUST resolve. If that file is missing, stop — the plugin is installed incorrectly.

## Why this skill exists

Behavior-first design starts at "build a command palette" — but that's wrong if you don't yet know which surfaces your product needs, what objects live on them, how users navigate between them, and what should be always-visible vs. tucked away. This skill walks the upstream decisions (Intent → Structure → Flows → Disclosure) one gate at a time and terminates in a handoff spec that `behavior-first-design` consumes, one surface at a time.

## Layer model

```
Intent  →  Structure  →  Flows  →  Disclosure  →  (handoff spec)  →  behavior-first-design
```

- **Intent** (Garrett Strategy plane / JTBD) — who, why, the one thing the product optimizes
- **Structure** (Brown IA, OOUX/ORCA, verb-back-derivation, cognitive-vs-business reconciliation) — derive objects from the user's mental model first; peer comparison is a sanity check after derivation, not the source
- **Flows** — navigation between surfaces, entry points, global chrome
- **Disclosure** — always-visible vs. 1-click vs. 2+click

Per-layer canon, full dialogue questions, options patterns, and gate criteria live in `references/layers/{intent,structure,flows,disclosure}.md`. Load the relevant file when entering a layer — do not inline its contents here.

## Dialogue shape

One question at a time. Lead with a recommended default ("my lean is B because…") rather than neutral option lists. Gate between layers with the verbatim transition prompts under **Gates** below. The full sequence:

1. Intent (see `references/layers/intent.md`)
2. After Intent gate, hand off to `visual-design` (sibling skill, runs between this skill and `behavior-first-design` per v0.3.0 pipeline). visual-design walks Mood → Typography → Color → Spacing → Motion → Polish; produces a visual spec at `<project-root>/docs/visual-design/<date>-<slug>.md` that downstream skills consume.
3. Structure (see `references/layers/structure.md`)
4. Flows (see `references/layers/flows.md`)
5. Disclosure (see `references/layers/disclosure.md`)
6. Write handoff spec to `<project-root>/docs/product-architecture/<YYYY-MM-DD>-<slug>.md`
7. Optional handoff kickoff of `behavior-first-design`, one surface at a time

Per-layer question lists, recommended defaults, and options are NOT duplicated in this file — open the relevant layer file when you enter that layer.

**Brainstorming-exit suggestion at Intent Q1:** if the user's prompt has fewer than ~3 of `{JTBD signal, persona signal, scope/exclusion signal}` after first context scan, the skill surfaces a brainstorming suggestion before Intent Q1:

> Your prompt is a starting point but lean on detail. Three signals seed Intent derivation: JTBD (what work? when? what time pressure?), persona (who? tool-adjacency?), scope-as-feature (what does this product refuse to be?). I see [<which signals are present>]; missing [<which>]. Recommend running superpowers:brainstorming first to enrich the prompt, then resume here at Intent Q1. Or proceed now with thinner derivation. (brainstorming / proceed)

If user picks `brainstorming`, skill exits cleanly. PA picks back up with brainstorming output in user's prompt context — restart, not full layer-state resume (full resume is v1.1). If user picks `proceed`, PA continues with thinner derivation; flags in `decisions log`: *"Intent derived from incomplete signals; JTBD/persona/scope not all captured. Spec quality reflects this."*

## Canonical references

Canon lives in `references/canon/` (five files): Brown's 8 IA Principles, OOUX (Prater), Garrett's 5-plane Elements model, Polaris IA Foundations, Christensen JTBD. SKILL.md does not summarize them — load a canon file when you need to cite the principle verbatim. Cross-cutting interaction laws (Hick, Miller, Jakob, Tesler, Fitts, Peak-End) live in the sibling skill at `behavior-first-design/references/foundations.md`.

## Handoff spec format

Markdown + YAML front-matter. Front-matter is machine-readable (`project`, `date`, `domain`, `domain_peers`, `intent.jtbd`, `intent.persona`, `intent.primary_job`). Body has four H2 sections matching the layers (Intent, Structure, Flows, Disclosure) plus a handoff table that routes each surface to the `behavior-first-design` components it needs. Full template with examples: `references/handoff-spec-template.md`.

The handoff table is the contract with `behavior-first-design` — every component name in it MUST correspond to a real `behavior-first-design/references/components/<name>.md` file.

## Domain awareness

At the Intent gate, the user names a domain (crm, project-mgmt, analytics, communication, storage, etc.) and 2-3 reference products. That tuple governs which peers the skill cites at Structure, Flows, and Disclosure. The minimum viable domain list and the "cross-domain surprise offer" mechanic live in `references/domains.md`. If the user can't name peers, fall back to the heuristic in that file.

## Gates and the 8-principle discipline

<HARD-GATE>
Do NOT invoke `behavior-first-design` or any other implementation skill, write any code, scaffold any project, generate any component file, or take any implementation action until the handoff spec is written to disk AND the user has explicitly approved it. This applies to EVERY project regardless of perceived simplicity. The handoff spec is the gate. If the user pushes to skip ahead, remind them that the spec exists so the downstream scaffolding has something concrete to route against — without it, behavior-first-design has no surface inventory to work from.
</HARD-GATE>

Verbatim transition prompts — emit word-for-word at each layer boundary, no paraphrasing:

- Intent → Structure: `"Intent captured. (Layer 1 of 4 in product-architecture; skill 1 of 3 in product-design pipeline.) Ready to move to Structure, or should I revise? (yes / revise)"`
- Structure → Flows: `"Structure captured. (Layer 2 of 4 in product-architecture; skill 1 of 3 in product-design pipeline.) Ready to move to Flows, or should I revise? (yes / revise)"`
- Flows → Disclosure: `"Flows captured. (Layer 3 of 4 in product-architecture; skill 1 of 3 in product-design pipeline.) Ready to move to Disclosure, or should I revise? (yes / revise)"`
- Disclosure → spec-write: `"Disclosure captured. (Layer 4 of 4 in product-architecture; skill 1 of 3 in product-design pipeline.) Ready to write the structural spec, or should I revise? (yes / revise)"`

(Each per-layer reference file also carries its own verbatim transition prompt — the list above is the canonical copy; the per-layer files exist so the prompt is next to its questions.)

Additional discipline: one question at a time; lead with a recommended default; self-review before writing the spec (every layer block filled, every handoff-table component name valid, every cross-reference resolvable).

## When NOT to invoke

- Single-component scope ("build a command palette", "add a status pill") → `behavior-first-design` directly
- Roadmap / prioritization / sequencing a backlog → out of scope
- Pure visual styling, tokens, or brand expression → `visual-design` (sibling skill in this plugin) for the spec; `frontend-design` for execution
- Data model, sync strategy, plugin lifecycle, auth → separate `data-architecture` skill (later)
- The product already has a stable IA and the user wants one surface reworked → skip to `behavior-first-design` with that surface as the scope

## Related skills

- `visual-design` (sibling, downstream) — consumes structural spec; produces visual spec with CSS tokens that downstream consumers use as upstream truth
- `behavior-first-design` (sibling, downstream) — consumes structural + visual specs; produces behavior spec that `superpowers:writing-plans` consumes
- `superpowers:brainstorming` (upstream) — refine bare prompts before this skill (suggested from Intent Q1 when prompt is thin)
- `superpowers:writing-plans` (downstream chain) — runs after the 3 specs are written; converts to implementation plan
- See `PIPELINE.md` at plugin root for the full 9-stage canonical pipeline
