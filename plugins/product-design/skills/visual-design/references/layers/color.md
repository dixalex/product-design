# Color

Pick color scheme + accent + neutral palette via semantic-contrast derivation; emit color tokens with verified accessibility floors.

## Canon

- `canon/refactoring-ui.md § Lightness scales not flat hues` — every accent needs a full 50→900 lightness scale; a single flat hex is not a token system. Scale steps cover interactive, disabled, and surface-tint uses without new hue decisions.
- `canon/refactoring-ui.md § Don't use grey when you mean color-with-low-saturation` — pure neutral grays read "dead" on dark bg. Chroma-tinted neutrals (cool cast on blue-accent products, warm cast on amber-accent products) unify the palette without adding saturation.
- `canon/apple-hig-foundations.md § Color` — semantic roles (background, label, accent, destructive) decouple palette from surface. Hard floors: 4.5:1 body text (WCAG AA), 3:1 large text and non-text elements, 7:1 for AAA / high-contrast users.
- `voices/linear.md § Color` — single-accent + near-grayscale. Desaturated electric blue against near-black; no secondary hues. Every deviation from grayscale is load-bearing.
- `voices/stripe.md § Color` — slate-tinted neutrals + targeted gradient. The blue cast ties every neutral back to the accent; gradients appear once then stop.
- `voices/operate.md § Color` — hand-named multi-color palette: graphpaper green, ember orange, blueberry, copper. A warm-refined mood licenses this breadth; a terminal-adjacent mood would reject it.

## Derivation method

**Named method: semantic-contrast derivation.** Three signals, read in order before proposing any option.

**Signal 1 — Mood emotional valence → hue family.**

| Mood family | Hue family |
|---|---|
| calm / utilitarian / terminal-adjacent | Cool — electric blue, slate |
| warm / craft-visible / collaborative | Warm — amber, orange, copper, graphpaper green |
| expressive / high-energy | High-saturation single point |
| refined-restraint / editorial | Desaturated neutrals + one accent |

**Signal 2 — Refinement-vs-conviction → saturation level.**

Refined moods → low saturation (density carries hierarchy). Conviction moods → mid saturation (color carries warmth). Expressive → high saturation at one vivid point, everything else neutral. Saturation derives from the mood's position on this axis; it is not a style preference.

**Signal 3 — Mode bias → chroma in neutrals.**

Dark-first products need chroma-tinted neutrals: pure `#1a1a1a` reads "dead"; a cool cast (`hsl(220, 6%, 10%)`) reads intentional. Light-first products tolerate purer whites but benefit from a slight cast tying neutrals to the accent. Both-mode products split into `--color-bg` / `--color-surface` semantic layers so shades swap without touching accent tokens.

**Anti-pattern: Tailwind defaults or purple gradients on white.** Both are AI-generated tells. The skill MUST derive hue from the mood phrase before naming any color.

**Worked examples:** Pine IRM ("terminal-adjacent / dense / calm"): cool hue + low saturation + dark-first → `hsl(220, 8%, 6%)` bg, cool-tinted neutrals, single accent. Operate ("warm-refined / craft-visible"): warm hue + mid saturation + light-first → graphpaper green, ember orange, blueberry, copper — multiple hand-named hues.

## Required output

- `color_scheme:` frontmatter — scheme + accent strategy in one phrase (e.g. `"dark-first; single desaturated electric blue accent"`).
- `## Color` prose paragraph — palette rationale in the user's words, citing mood phrase, derivation signals, and voice precedents.
- Tokens block — backgrounds (`--color-bg`, `--color-surface`, `--color-surface-raised`), foreground (`--color-fg`, `--color-fg-muted`, `--color-fg-subtle`), accent scale `--color-accent-50` through `--color-accent-900` + aliases, status colors, borders. All HSL, values confirmed by Question 4 floors.

## Dialogue questions

Four questions; skill waits for answer before advancing.

**Q1 — Scheme commitment.**

> "Mood = `<committed mood phrase>`. Color-scheme options: dark-first, light-first, both. For 'both,' which is load-bearing? My lean: `<scheme>` because `<mode-bias derivation>`. Pick."

Expected: dark-first / light-first / both. After pick, cite peers.

**Q2 — Accent-color derivation.**

> "Hue family from mood: `<cool | warm | expressive | restrained>`. Saturation from refinement: `<low | mid | high>`. My lean: accent = `<hex or HSL>`. Alternates: `<option B — one step warmer>`, `<option C — one step more restrained>`. Pick or override."

Expected: A/B/C, hex, or HSL. After pick, cite canonical voices.

**Q3 — Neutral palette chroma.**

> "Refactoring UI's thesis: chroma beats pure gray. Options: (a) cool-chroma neutrals — slight blue cast; (b) warm-chroma neutrals — slight amber cast; (c) pure grayscale — technical-restraint commitment like Linear. My lean: `<a/b/c>` because `<1-line derivation>`. Pick."

Expected: a / b / c. Chroma is low in all cases — direction of tint, not saturation.

**Q4 — Accessibility floors.**

> "Verifying contrast: body text on bg = `<X.X:1>` (target ≥4.5:1 AA; ≥7:1 AAA). Large text on bg = `<X.X:1>` (target ≥3:1). Accent on bg = `<X.X:1>` (target ≥3:1). If any fall short: (a) lighten/darken accent; (b) increase fg saturation; (c) accept AA-only with explicit acknowledgment. Pick."

Expected: a / b / c, or floors confirmed. Skill updates token values before gate close.

## Constraint surfacing

Surface before Q1 when the spec flags:

- **Low-vision personas.** Raise to 7:1 AAA. Forces near-white fg; shifts accent scale toward 300–400 for interactive elements.
- **High-saturation accent on dark bg.** Halation risk for astigmatism users (~30–40%); desaturate accent 10–20 points.
- **Color-blind audiences.** Status differentiation needs icons + text labels, not color alone. Annotate status tokens with `/* icon required */`.
- **Virtualized dense tables.** Row alternation = 2–3% lightness shift on `--color-surface`, not a new neutral hue.

## Options pattern

Derivation-based first: hue-from-mood → saturation-from-refinement → mode-bias-from-brief chain, then a proposed hex/HSL. After the user commits, cite peers as convention precedent in one sentence — never as source. Two alternates alongside the lean (one step warmer, one step more restrained); "override with your own hex/HSL" is always a fourth path.

## User-voice prompt

After peer comparison following Question 2:

> "Any color reference beyond the canonical voices? Drop a name, palette URL, or hex value."

Captured references queue for research at spec-write time — do NOT fetch during dialogue. Voice files land at `<project-root>/docs/visual-design/voices/<slug>.md`. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`canon/refactoring-ui.md § Lightness scales not flat hues`, `canon/refactoring-ui.md § Don't use grey when you mean color-with-low-saturation`, `canon/apple-hig-foundations.md § Color`, `voices/linear.md § Color`, `voices/stripe.md § Color`, `voices/operate.md § Color`, `anti-patterns.md § Color anti-patterns`.

## Gate criteria

- [ ] Scheme committed (dark-first / light-first / both with named primary; in `color_scheme:` frontmatter)
- [ ] Accent + neutral palette committed (accent HSL with full 50–900 scale; chroma direction confirmed for neutrals)
- [ ] Accessibility floors verified (body text ≥4.5:1, large text ≥3:1, accent on bg ≥3:1; AAA upgrade noted if low-vision flag present)
- [ ] Tokens emitted (`--color-bg`, `--color-surface`, `--color-fg`, `--color-fg-muted`, `--color-accent` through full scale, status colors, `--color-border`)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

Color captured. Ready to move to Spacing, or should I revise? (yes / revise)

## Mid-session checkpoint

After the transition prompt, the skill also emits:

```
Brief in progress saved to <project-root>/docs/visual-design/<date>-<slug>.md (incomplete). Resume now with Spacing → Motion → Polish, or pause and continue in a new session?
```

If the user pauses: write `# Visual brief — IN PROGRESS` at the top of the spec file. On resume: read the in-progress brief, confirm committed layers (Mood, Typography, Color), and continue at Spacing without re-running earlier questions.
