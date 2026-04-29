---
name: visual-design
description: "Pre-behavior aesthetic decisions for a product — visual spec written before component behavior is resolved. Guided dialogue through Mood, Typography, Color, Spacing, Motion, Polish layers. Reads the structural spec from product-architecture and produces a visual spec (prose decisions + CSS tokens) that behavior-first-design and frontend-design consume. Use when the user says \"design the look,\" \"visual styling,\" \"typography pass,\" \"color system,\" \"aesthetic direction,\" \"make it feel like Linear / Stripe / Operate,\" or \"brand expression.\" NOT for component scaffolding (use behavior-first-design); NOT for code generation (use frontend-design after the spec is written)."
---

# visual-design

**Requires sibling skills `product-architecture` (upstream) and `behavior-first-design` (downstream)** — co-installed in the `product-design` plugin. The structural spec from product-architecture seeds Mood derivation; the visual spec produced here is consumed by behavior-first-design's emitted code (via CSS custom properties) and passed to frontend-design as explicit context. If product-architecture's brief is missing, the dialogue can run cold but quality degrades.

## Why this skill exists

Visual aesthetic decisions need a deliberate decision phase upstream of code generation, not improvised by `frontend-design` at execution time. Without it, the same product designed in two sessions produces two different aesthetics with no decision trail. This skill walks Mood → Typography → Color → Spacing → Motion → Polish as gated layers and terminates in a visual spec that downstream skills consume as upstream truth.

## Layer model

```
Mood  →  Typography  →  Color  →  Spacing  →  Motion  →  Polish  →  (visual spec written)
  ↑       (mood-derived)  (mood + a11y)  (mood)  (behavior+mood)  (residual)
reads structural spec
extracts mood candidates
                                   │
                                   ↓
                        behavior-first-design's emitted code consumes via CSS custom properties
                        frontend-design receives brief as explicit context at invocation time
```

Per-layer canon, derivation method, dialogue questions, options pattern, and gate criteria live in `references/layers/{mood,typography,color,spacing,motion,polish}.md`. Load the relevant file when entering a layer — do not inline its contents here.

**Mood is special** — Q0-equivalent. Without a Mood commitment, every later layer is arbitrary.

**Polish is special** — splits into (a) depth system (shadow / border / focus ring; derives from Spacing) and (b) signature details (gradient / grain / cursor — the residual after upstream layers commit).

## Dialogue shape

One question at a time. Lead with a recommended default ("my lean is X because…"). Skill proposes 1–3 derivation-based options at each layer (NOT peer-shapes; peer voices are cited as precedent AFTER the user picks). Per-layer transition prompts are verbatim — see Gates.

**Mid-session checkpoint after Color (layer 3):** *"Brief in progress saved. Resume now with Spacing → Motion → Polish, or pause?"* — opt-in pause for fatigue.

**Constraint surfacing (per spec § 2.5):** at start of each layer, skill reads structural spec for performance/input constraints (virtualization, keyboard-primary, high-frequency hover) and surfaces them as floors that mood-derived choices adjust within.

**User-supplied voices — runtime mechanics:**

1. *Capture during dialogue:* at every layer's peer-comparison step, skill asks: *"Any reference beyond the canonical voices? Drop a name or URL."* Capture each as `{name, url, layer_first_cited}` into an in-session `user_voices_queue`.
2. *Defer research:* do NOT fetch during dialogue (would break the one-question-at-a-time rhythm). Queue grows across layers.
3. *Resolve at spec-write time:* before writing the visual spec, iterate `user_voices_queue`. For each entry:
   - Use `WebFetch` (or `firecrawl` if available) on the URL.
   - Extract aesthetic profile + per-layer treatment per Voice template.
   - Write to `<project-root>/docs/visual-design/voices/<slug>.md` (slug = lowercase + hyphenated).
4. *Fallback if web access blocked* (sandbox / offline): write a stub voice file with `{name, url, captured: <date>, status: "research deferred — web access blocked"}` and surface to user: *"I couldn't fetch <url>; voice file written as stub. Resolve manually or in a session with web access."* Brief still ships; user_voices frontmatter lists the slug.
5. *Never auto-promote to plugin canon.* Project-local only.
6. *Frontmatter `user_voices` field* lists the slugs of resolved voice files; structurally validates at spec-write.

## Canonical references

Canon lives in `references/canon/` (4 files): Refactoring UI (Wathan + Schoger), Rams 10 Principles, Lupton *Thinking with Type*, Apple HIG Foundations. Voices in `references/voices/` (4 files): Linear, Stripe, Operate, Rauno-micro-details. SKILL.md does not summarize canon/voices — load when needed at the relevant layer.

## Handoff spec format

Markdown + YAML frontmatter + CSS custom-properties block. See `references/handoff-spec-template.md` for the full template.

Spec lands at `<project-root>/docs/visual-design/<YYYY-MM-DD>-<slug>.md`. The CSS tokens block is *upstream truth* — user passes it explicitly to frontend-design as context; behavior-first-design's emitted code consumes via `var(--*)` references.

**v0.3.0 backward-compat (until v0.5.0):** The skill accepts both legacy `*_brief:` and new `*_spec:` frontmatter keys when reading upstream specs. Specifically: `visual_brief:` / `visual_spec:`, `structural_brief:` / `structural_spec:`, `constraints_from_structural_brief:` / `constraints_from_structural_spec:`. Title text matches both `# <project> — <kind> brief` and `# <project> — <kind> spec` H1 patterns. v0.5.0 drops legacy support after one-version grace.

## Domain awareness

Inherited from structural spec. visual-design does not re-ask domain — it reads `domain` and `domain_peers` from the structural spec frontmatter and uses both to seed Mood candidates and to scope which canonical voices fire (e.g., dev-tool domain → Linear voice prominent; commerce domain → Stripe voice prominent).

## Gates and discipline

<HARD-GATE>
Do NOT invoke `behavior-first-design`, `frontend-design`, or any other implementation skill, write any code, scaffold any project, or take any implementation action until the visual spec is written to disk AND the user has explicitly approved it. This applies to EVERY project regardless of perceived simplicity. The brief is the gate. If the user pushes to skip ahead, remind them that the spec exists so downstream code has tokens to reference and components to align with — without it, frontend-design freelances the aesthetic and behavior-first-design has nothing to compose against.
</HARD-GATE>

Verbatim transition prompts — emit word-for-word at each layer boundary, no paraphrasing:

- Mood → Typography: `"Mood captured. (Layer 1 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to move to Typography, or should I revise? (yes / revise)"`
- Typography → Color: `"Typography captured. (Layer 2 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to move to Color, or should I revise? (yes / revise)"`
- Color → Spacing: `"Color captured. (Layer 3 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to move to Spacing, or should I revise? (yes / revise)"` (also: mid-session checkpoint prompt fires here)
- Spacing → Motion: `"Spacing captured. (Layer 4 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to move to Motion, or should I revise? (yes / revise)"`
- Motion → Polish: `"Motion captured. (Layer 5 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to move to Polish, or should I revise? (yes / revise)"`
- Polish → spec-write: `"Polish captured. (Layer 6 of 6 in visual-design; skill 2 of 3 in product-design pipeline.) Ready to write the visual spec, or should I revise? (yes / revise)"`

(Each per-layer reference file also carries its own verbatim transition — the list above is canonical; per-layer files put the prompt next to its questions.)

## When NOT to invoke

- Component scaffolding ("build a list row", "add inline edit") → `behavior-first-design`
- IA / surface decisions / object model ("design a CRM") → `product-architecture`
- Code generation / scaffolding execution → `frontend-design` (Anthropic official) AFTER the spec is written
- Brand identity (logo, marketing copy, illustration) — out of scope; this skill is product-aesthetic, not brand-aesthetic
- Print / email / out-of-product surfaces — out of scope (web product surfaces only at v1)

## Related skills

- `product-architecture` (sibling, upstream) — produces structural spec that this skill reads
- `behavior-first-design` (sibling, downstream) — consumes the visual spec; binds CSS tokens to behavior contracts at Layer 6
- `frontend-design` (Anthropic official, downstream execution) — consumes visual spec as upstream truth at execution time; visual spec tokens are upstream truth, frontend-design implementation is best-effort
- `superpowers:writing-plans` (downstream chain) — runs after the 3 specs are written
- See `PIPELINE.md` at plugin root for the full 9-stage canonical pipeline
