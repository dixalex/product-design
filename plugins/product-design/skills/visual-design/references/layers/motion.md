# Motion

Pick duration baselines and easing per surface category via behavior-frequency × mood-energy matrix; emit motion tokens; defer fire-policy to behavior-first-design.

## Canon

- `behavior-first-design/references/motion.md § Frequency-novelty matrix` — **fire-policy authority.** When motion fires (and when it doesn't) is decided here, not by this layer. This layer inherits that policy and adjusts duration values and easing curves within it.
- `tokens/motion-defaults.md` — duration values migrated at v0.3.0: instant 0ms, fast 150ms, medium 180ms, base 200ms. This layer's dialogue proposes overrides; silent acceptance inherits defaults.
- `canon/apple-hig-foundations.md § Motion` — meaningful motion communicates state or spatial relationship; reduced-motion compliance is non-negotiable for all users, not just low-vision personas.
- `voices/rauno-micro-details.md § Motion` — motion-as-communication, not motion-as-decoration. Entry elements ease out; exiting elements ease in. Duration tiered by register. `prefers-reduced-motion` wired in at authoring time, not retrofitted.
- `anti-patterns.md § Motion anti-patterns` — no spring/bounce in product UI; never animate frequent actions; never skip `prefers-reduced-motion`.

## Derivation method

**Named method: behavior-frequency × mood-energy matrix.**

Two axes intersect to determine what this layer emits. The fire-policy axis (whether to animate at all) belongs to `behavior-first-design/references/motion.md § Frequency-novelty matrix`. This layer contributes the energy axis (how long and how curved the animation is when it fires).

**Axis 1 — Behavior frequency (from behavior brief via behavior-first-design).**

Read the behavior brief for the project surface. Classify each interaction using behavior-first-design's matrix: high-frequency (≥50/day, low novelty) → locked at 0ms regardless of mood. The matrix is the floor; this layer's choices adjust within it, not against it. If the behavior brief flags high-frequency hover-reveal surfaces, motion is locked to 0ms — mood cannot override frequency on this axis.

**Axis 2 — Mood energy (from Mood layer).**

Read the committed mood phrase from the Mood layer.

| Mood energy | Duration direction | Easing permitted |
|---|---|---|
| calm-restraint (dense, utilitarian) | Conservative; inherit defaults unmodified | ease-out / ease-in; no additional curves |
| expressive-conviction (warm-refined, craft-visible) | Bolder on rare surfaces; 200–300ms; frequency axis still wins | ease-out + optional signature curve on celebratory states |

**Matrix output — four cells:**

| Frequency | Mood energy | Duration | Rule |
|---|---|---|---|
| Frequent (≥50/day) | Calm | 0ms | Instant; frequency wins |
| Frequent (≥50/day) | Expressive | 0ms | Instant; frequency wins even in expressive products (Rauno) |
| Rare / boundary-marking | Calm | 150–180ms ease-out | Dialog / popover enter; toast enter; row slide-in |
| Rare / boundary-marking | Expressive | 200–300ms ease-out; variable curves permitted | Peek / drawer enter; content entry; success states |

**Worked example — Pine IRM, mood = "calm-dense."** Frequent actions (keyboard nav, row-select, hover chips) locked at 0ms. Rare boundary-markers: peek/drawer enter 200ms ease-out (`tokens/motion-defaults.md § base`); toast enter 150ms (`§ fast`). No signature curve. Reduced-motion: all durations → 0.01ms (preserves `transitionend`).

**Worked example — Operate, mood = "warm-refined / craft."** Frequent actions still 0ms. Rare surfaces inherit defaults. Expressive mood licenses one addition: `--ease-elegant` (cubic-bezier overshoot) on celebratory states (success, onboarding completion). Otherwise defaults unchanged.

**Anti-patterns:** spring physics in product UI; animating frequent actions regardless of mood; ignoring `prefers-reduced-motion`; adding signature curves to boundary-marker animations — signature curves are Polish-tier, not Motion-tier.

## Required output

The Motion layer populates two artifacts in the handoff brief:

- `motion:` frontmatter field — `<minimal | expressive>; <duration baseline> ease` (e.g. `minimal; 150ms ease-out`).
- `## Motion` prose paragraph — the committed duration baselines, easing rationale, reduced-motion contract, and any signature curves, citing both `tokens/motion-defaults.md` and `behavior-first-design/references/motion.md § Frequency-novelty matrix`.
- Motion tokens block — emitted at gate close: `--duration-instant`, `--duration-fast`, `--duration-medium`, `--duration-base`, `--ease-out`, `--ease-in`; optionally `--ease-elegant` (or equivalent named curve) if the mood is expressive and a signature curve was committed.

## Dialogue questions

Three questions; skill waits for the user's answer before advancing.

**Question 1 — Duration baselines.**

> "Per `tokens/motion-defaults.md`, defaults are: instant 0ms (keyboard nav, hover-reveal, row-select), fast 150ms (dialog / popover / tooltip enter, toast enter), medium 180ms (row slide-in), base 200ms (peek / drawer enter, new content). My lean: inherit defaults unmodified. Override any?"

Expected: "inherit" or a specific override (e.g., "base → 250ms"). After the user commits, cite peers as convention precedent: "Linear inherits defaults at calm; Operate inherits defaults but may add `--ease-elegant` for the Polish tier; Stripe inherits defaults with longer durations on Press surfaces — though that's a brand-specific departure, not a general rule."

**Question 2 — Easing per surface category.**

> "Easing: ease-out for entering elements, ease-in for exiting, linear for indeterminate progress. NEVER spring or bounce in product UI. Mood = `<picked>`. If expressive, optionally add a signature curve (e.g., `--ease-elegant: cubic-bezier(...)` for slight overshoot on celebratory states). My lean: `<inherit defaults / add signature curve>`. Pick."

Expected: "inherit" or a named curve with a `cubic-bezier()` value. Signature curves apply only to Polish-tier celebratory states — not to dialog, peek, or drawer enters.

**Question 3 — Reduced-motion contract.**

> "`prefers-reduced-motion: reduce` overrides: durations → 0.01ms (preserves `transitionend`), spatial transforms → opacity-only fades. Non-negotiable. Acknowledge."

Expected: "acknowledged." No negotiation on this question. The reduced-motion contract is unconditional — vestibular disorders, migraine triggers, and attention sensitivities are non-negotiable design constraints.

## Constraint surfacing

Surface before Question 1 when the behavior brief flags:

- **High-frequency hover-reveal surfaces (e.g., row-action chips on `pointerenter`).** Motion is locked to 0ms on those surfaces regardless of mood energy. Name the specific surface so the user understands the constraint before reviewing duration options.
- **Keyboard-primary product.** The keyboard-nav 0ms floor applies across the board. An expressive mood cannot animate keyboard-triggered state changes.
- **Low-frequency, high-novelty first-time experiences (onboarding, empty states).** These fall outside the matrix's standard cells — motion is permitted up to 300ms. Surface as an explicit exception.

## Options pattern

Derivation-based first: frequency-axis → energy-axis → matrix cell → proposed duration and easing. The skill names the matrix cell before proposing any value. After commit, cite peers as convention precedent:

- **Linear** — calm; inherits defaults; no signature curve.
- **Operate** — expressive / craft; inherits defaults; adds `--ease-elegant` for celebratory Polish tier.
- **Stripe** — editorial; inherits defaults; longer durations on marketing Press surfaces (brand-specific departure).
- **Rauno's products** — hand-tunes duration by register (UI chrome 150ms, content 250ms); craft-visible teams only.

Framing: "Linear lands here for similar reasons" — not "pick something like Linear." Derivation stays primary.

## User-voice prompt

After peer comparison following Question 1:

> "Any motion reference beyond the canonical voices? Drop a name, demo URL, or specific `cubic-bezier()` curve."

Captured references queue for research at brief-write time — do NOT fetch during dialogue. Voice files land at `<project-root>/docs/visual-design/voices/<slug>.md`. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the brief.

## Principles invoked

`behavior-first-design/references/motion.md § Frequency-novelty matrix`, `tokens/motion-defaults.md`, `canon/apple-hig-foundations.md § Motion`, `voices/rauno-micro-details.md § Motion`, `anti-patterns.md § Motion anti-patterns`.

## Gate criteria

- [ ] Duration baselines committed (instant / fast / medium / base; inherited or overridden per `tokens/motion-defaults.md`)
- [ ] Easing committed (ease-out / ease-in baseline; signature curves named and scoped if any)
- [ ] Reduced-motion contract acknowledged
- [ ] Tokens emitted (`--duration-instant`, `--duration-fast`, `--duration-medium`, `--duration-base`, `--ease-out`, `--ease-in`; optionally `--ease-elegant` or equivalent)

## Transition prompt

Motion captured. Ready to move to Polish, or should I revise? (yes / revise)
