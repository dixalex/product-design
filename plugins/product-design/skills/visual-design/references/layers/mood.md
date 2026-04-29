# Mood

Commit to an aesthetic mood phrase derived from the structural spec; every later layer hangs off this.

## Canon

- `canon/rams-10-principles.md § 3 (aesthetic)` — aesthetic discipline is functional; coherent mood makes a product easier to use, not just prettier.
- `canon/refactoring-ui.md § Hierarchy via size + weight` — conviction beats neutrality; a held mood produces clearer hierarchy than hedging across tones.
- `canon/apple-hig-foundations.md § Color + Materials` — system colors carry semantic mood; mood is a load-bearing structural decision, not decoration.
- `voices/linear.md § Mood` — calm-dense exemplar; the reference point for tool-adjacent, low-distraction vocabulary.
- `voices/operate.md § Mood` — refined-warm exemplar; restraint and warmth coexisting, for collaborative personas.
- `voices/stripe.md § Aesthetic profile` — editorial-trustworthy exemplar; confident type, deliberate whitespace, institutional trust.

## Derivation method

**Named method: structural-brief signal extraction.**

Mood emerges from three signals present in every structural spec. The skill reads them in this order:

**Signal 1 — JTBD emotional verbs.** Extract verbs and adverbs literally. "Triage faster than my caffeine wears off" yields: work, triage, fast. High-frequency action verbs under time pressure signal calm-utilitarian-dense, not playful or aspirational.

**Signal 2 — Persona tool-adjacency.** A keyboard-first solo founder lives in terminal and editor contexts — that adjacency licenses a terminal-adjacent vocabulary (monospace accents, monochrome base, tight rows). A click-primary SMB team licenses a friendlier vocabulary (rounded corners, warmer accent, more generous row height).

**Signal 3 — Scope-as-feature exclusions.** What the product refuses signals as much as what it includes. "No manager dashboards, no AI-summary cards" → restraint, dense-not-generous. "No spreadsheet density" → more generous than a data grid. Exclusions intersect to triangulate a region of the mood space.

**Anti-pattern: peer-mimicry.** Without derivation, the dialogue shows voice references first. The user picks from peers without asking whether that peer's structural spec matches theirs. Derivation forces "what does THIS brief imply?" before peer comparisons appear. Voices enter only after the user picks — as precedent evidence, not source.

**Worked example — Pine IRM hypothetical:**

- JTBD: "When working through morning leads, I want to triage faster than my caffeine wears off."
- Persona: solo founder, keyboard-first.
- Scope exclusions: no manager dashboards, no AI-summary cards, no rich charts.

Applying the three signals:
- Emotional verbs: work, triage, fast → calm-utilitarian; speed and economy, not delight or aspiration.
- Tool-adjacency: keyboard-first solo founder → terminal-adjacent vocabulary is licensed.
- Exclusions: no dashboards, no summary cards → dense-not-generous; the product does not need to explain itself with charts.

Intersection narrows to three candidate phrases:
- (a) "terminal-adjacent / dense / calm"
- (b) "monastic / focus / quiet"
- (c) "tool-not-app / utilitarian / refined"

The skill's lean: (a), because the JTBD's time-pressure verbs point more toward dense-utilitarian than toward the quieter register of (b), and (c) risks being too abstract to pin downstream color and spacing decisions.

## Required output

The Mood layer populates two artifacts in the handoff spec:

- `mood:` frontmatter field — one phrase, 2–6 words, in the user's words after the dialogue resolves.
- `## Mood` prose paragraph — the aesthetic commitment in the user's words, citing the structural-brief excerpts that drove derivation. See `handoff-spec-template.md § Mood` for the expected shape: "JTBD's emotional verb 'X' + persona 'Y' + scope rejection of 'Z' → mood phrase."

## Dialogue questions

The Mood layer runs exactly two questions. The skill waits for the user's answer before advancing.

**Question 1 — Derivation and candidate presentation.**

Prompt shape:

> "Reading your structural spec: JTBD `<quote>`, persona `<quote>`, scope rejects `<list>`. The mood candidates I see: (a) `<phrase 1>`, (b) `<phrase 2>`, (c) `<phrase 3>`. My lean is `<a/b/c>` because `<derivation rationale — cite the specific verbs, tool-adjacency signal, and exclusions that produced this lean>`. Which lands, or override with your own phrase?"

Expected answer shape: a letter (a, b, or c) or a custom phrase of 2–6 words.

The skill MUST cite structural-brief excerpts in this prompt — naming candidates without quoting the spec is peer-mimicry in disguise. After the user picks, cite canonical voices as precedent: "Linear lands here for similar reasons; Operate lands one step warmer; Stripe lands two steps more editorial." Convention precedent (Jakob's Law), not source.

**Question 2 — Elaboration and commitment.**

Prompt shape:

> "Got it — `<picked mood>`. One line: what about this product makes that mood right?"

Expected answer shape: 1–3 sentences from the user.

Purpose: forces explicit commitment so every later layer has something load-bearing to hang off. This output goes verbatim into the `## Mood` prose paragraph in the handoff spec. The skill does not paraphrase or synthesize it — the user's words are the record.

## Constraint surfacing

At the start of the Mood layer, the skill checks the structural spec for performance and input flags per spec § 2.5. If present, it surfaces them before Question 1:

> "This brief flags `<flags>`. These pin some mood-derived choices: `<list of floors>`. The mood you pick adjusts within these constraints, not against them."

Examples of what to surface:

- Virtualized 100k-row list → density mode floor = compact (24–32px row); the mood cannot choose generous spacing for list rows.
- Keyboard-primary surface → motion must hold the keyboard-nav 0ms floor; a high-energy motion mood will conflict.
- Low-saturation mood risk → typography hierarchy must not depend on dense color differentiation alone; status colors need enough contrast to survive a near-monochrome palette.

Surface constraints before Question 1 so they are already in scope when the user evaluates candidates.

## Options pattern

Always derivation-based first. The skill leads with "my lean is X because…" and provides the derivation rationale before showing any peer reference. Rationale must cite the specific structural-brief signals (emotional verbs, tool-adjacency, exclusions) that produced the lean.

After the user picks, the skill cites canonical voices as precedent evidence:

- Linear — calm-dense exemplar; lands at the utilitarian-focus end of the space.
- Operate — refined-warm exemplar; one step warmer and more collaborative than Linear.
- Stripe — editorial-trustworthy exemplar; two steps more editorial, with deliberate whitespace and institutional confidence.

The framing is always "Linear lands here for similar reasons" — not "pick something like Linear." Derivation stays primary.

## User-voice prompt

At the end of the Mood layer, after Question 2, the skill asks:

> "Any reference beyond the canonical voices that captures the mood you want? Drop a name or URL."

Captured references queue for research at spec-write time — do NOT fetch during dialogue. Voice files land project-local at `<project-root>/docs/visual-design/voices/<slug>.md`. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`canon/rams-10-principles.md`, `canon/refactoring-ui.md`, `canon/apple-hig-foundations.md`, `voices/linear.md`, `voices/operate.md`, `voices/stripe.md`.

## Gate criteria

- [ ] Mood phrase committed (2–6 words; in the user's words after the dialogue resolves)
- [ ] 1-line elaboration written (user's answer to Question 2; goes verbatim into the Mood prose paragraph)
- [ ] Constraints surfaced (if the structural spec flagged performance or input constraints)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

Mood captured. Ready to move to Typography, or should I revise? (yes / revise)
