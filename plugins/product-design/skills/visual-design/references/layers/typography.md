# Typography

Pick display + body fonts via voice-personality mapping; emit type tokens (`--font-sans`, `--font-mono`, `--text-base`, scale).

## Canon

- `canon/lupton-thinking-with-type.md § Letter` — Lupton's type-personality axis: humanist sans carry residual handwritten warmth; grotesque sans are geometric and neutral; transitional/modern serifs carry formal contrast; display faces carry high-character energy. The axis is the selection mechanism — not aesthetic preference.
- `canon/refactoring-ui.md § Hierarchy via size + weight` — hierarchy is built from size and weight, not color. A type scale with deliberate ratio produces visual hierarchy without relying on color differentiation; skipping the scale produces flat, arbitrary-feeling layouts.
- `canon/apple-hig-foundations.md § Typography` — system text styles (Large Title through Caption) define a hierarchy that Dynamic Type scales; the principle: name every text role, never use arbitrary px values for role-less text.
- `voices/stripe.md § Typography` — Stripe runs an opinionated three-typeface system (display, body, mono); each face has a declared role. The discipline of three roles is the signal, not the specific fonts.
- `voices/operate.md § Typography` — Muoto Variable at 13.5px base; a bespoke variable font that carries visible craft signal. Operate's choice demonstrates that "warm-refined / craft-visible" mood licenses a higher-character face even at product density.
- `voices/linear.md` — Inter Display + Inter Variable at 13px base; the humanist-restraint exemplar. Inter's residual handwritten gestures read warm without being decorative, at the density Linear's brief requires.

## Derivation method

**Named method: voice-personality mapping.**

Lupton's type taxonomy maps to the committed mood signature. The skill reads the Mood phrase committed in the previous layer and selects the taxonomy class that fits. Without this mapping, the dialogue defaults to Inter on every project — the named anti-pattern in `anti-patterns.md § Typography anti-patterns`.

**Mapping table:**

| Mood family | Lupton taxonomy class | Exemplar faces | Character |
|---|---|---|---|
| calm / utilitarian / dense | Humanist sans | Inter, Söhne | Residual handwritten gestures; warm but not soft |
| technical / neutral / system | Grotesque sans | Helvetica Neue, GT America, Aktiv Grotesk | Geometric, neutral; no historical warmth |
| refined / editorial / trustworthy | Transitional or modern serif | Tiempos, Freight Display, Bodoni | Pronounced stroke contrast; formal authority |
| warm / craft-visible / collaborative | Humanist sans with high character | Muoto Variable, Canela Text | Visible craft signal; warmer than standard humanist |
| playful / expressive / marketing | Display / soft humanist | Clash Display, Cabinet Grotesk | High-contrast character; reserved for marketing surfaces only |

**Worked example:**

Pine IRM mood = "terminal-adjacent / dense / calm" — calm + technical intersect at humanist sans with restraint. Derivation yields Inter Display + Inter Variable (or Söhne if license allows). The humanist warmth registers at 13px density without adding the softness the mood rejects. One step warmer: Operate's Muoto Variable, earned by a "warm-refined / craft-visible" mood that explicitly licenses craft signal.

**Anti-pattern:** "Linear's typography because Linear" — peer-mimicry in disguise. The skill MUST derive the pairing from the mood first, then cite Linear as precedent after the user commits. Mood derivation is primary; peer reference is evidence.

## Required output

The Typography layer populates three artifacts in the handoff brief:

- `type_pairing:` frontmatter field — display font + body font, named explicitly (e.g. "Inter Display + Inter Variable").
- `## Typography` prose paragraph — the reasoning in the user's words, citing the mood phrase and voice-personality mapping that drove the choice.
- Typography tokens block — emitted at gate close: `--font-sans`, `--font-mono`, `--text-base`, `--type-scale`, plus role-named scale steps (`--text-xs` through `--text-4xl` or equivalent).

## Dialogue questions

The Typography layer runs exactly three questions. The skill waits for the user's answer before advancing.

**Question 1 — Display and body pairing, derived from mood.**

Prompt shape:

> "Mood = `<committed mood phrase>`. Voice-personality mapping → `<Lupton taxonomy class>` (reason: `<1-line rationale citing the mood signals>`). My lean: `<display font>` + `<body font>`. Alternates: `<option B>`, `<option C>`. Pick or override."

Expected answer: a letter (A/B/C) or a named font pair. After the user picks, the skill cites peers as confirmation: "Linear lands at Inter for the same reason; Operate one step warmer at Muoto Variable; Stripe one step more editorial with its three-face system." Never as source.

**Question 2 — Type scale ratio, derived from density commitment.**

Prompt shape:

> "Type scale options: 1.25 (minor third — generous, editorial; steps stay close together), 1.333 (perfect fourth — balanced default; works across most surfaces), 1.5 (perfect fifth — high-contrast; strong separation between display and body, attention-grabbing). For dense surfaces, 1.25 reads quieter and keeps the scale compact; for editorial surfaces, 1.5 separates display from body with authority. My lean: `<ratio>` because `<1-line derivation from mood and density>`. Pick."

Expected answer: 1.25 / 1.333 / 1.5 or a custom value.

**Question 3 — Monospace usage scope.**

Prompt shape:

> "Monospace usage: (a) chord chips + timestamps + numerical alignment only; (b) all technical surfaces — code blocks, data tables, inputs that accept commands; (c) none. The anti-pattern is monospace as 'I want to look technical' — pick deliberately based on what your product actually renders. My lean: `<a/b/c>` because `<1-line rationale>`. Pick."

Expected answer: a / b / c with optional elaboration.

## Constraint surfacing

Surface before Question 1 when the structural brief flags any of:

- **Virtualized list rows.** Line-height on row content locks at 1.3–1.4; 1.5 wastes row pixels. The body font adjusts within that floor.
- **Reduced-motion mode.** Variable-font weight transitions become instant under `prefers-reduced-motion`. The weight axis still earns static hierarchy; it cannot be a motion signal.
- **Multilingual.** German and inflected languages run ~30% longer than English. Multilingual surfaces use absolute-px line-height on constrained rows, not `em`; type scales that assume English string length will break.

## Options pattern

Derivation-based first. Name the Lupton taxonomy class before naming fonts; lead with "my lean is X because [mood + taxonomy]." Propose exactly two alternates alongside the lean — one step warmer, one step more neutral. After the user commits, cite peers as convention precedent in one sentence. The anti-pattern is reversing the order: showing Stripe or Linear first and working backward.

## User-voice prompt

After peer comparison following Question 1, the skill asks:

> "Any typography reference beyond the canonical voices? Drop a name, font foundry, or URL."

Captured references queue for research at brief-write time — do NOT fetch during dialogue. Voice files land project-local at `<project-root>/docs/visual-design/voices/<slug>.md`. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the brief.

## Principles invoked

`canon/lupton-thinking-with-type.md`, `canon/refactoring-ui.md § Hierarchy via size + weight`, `canon/apple-hig-foundations.md § Typography`, `voices/stripe.md § Typography`, `voices/operate.md § Typography`, `voices/linear.md`, `anti-patterns.md § Typography anti-patterns`.

## Gate criteria

- [ ] Display + body pairing committed (named fonts in `type_pairing:` frontmatter)
- [ ] Type scale ratio committed (1.25 / 1.333 / 1.5 or explicit custom value)
- [ ] Monospace usage scope committed (a / b / c)
- [ ] Tokens emitted: `--font-sans`, `--font-mono`, `--text-base`, `--type-scale`, role-named scale steps
- [ ] User has confirmed the layer's block in the handoff brief

## Transition prompt

Typography captured. Ready to move to Color, or should I revise? (yes / revise)
