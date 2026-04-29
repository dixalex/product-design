# Polish

Commit (a) depth system (shadow/border/focus-ring) derived from Spacing, and (b) one signature detail via distinctive-residual derivation; emit polish tokens; pose the final "what makes THIS feel like THIS" question.

## Canon

- `voices/rauno-micro-details.md § Polish` — details must pay rent; if a detail doesn't signal state, reinforce hierarchy, or communicate something the user needs, remove it.
- `canon/refactoring-ui.md § Depth requires intent` — every shadow tier must correspond to an elevation concept. No elevation meaning = noise.
- `canon/rams-10-principles.md § 8 (thorough to last detail)` — good design is thorough down to the last detail. Polish is where this lives.
- `canon/rams-10-principles.md § 10 (as little design as possible)` — counter-tension to § 8: remove the wrong thing with the same care used to add the right thing.
- `voices/linear.md § Polish` — 1px hairline borders, 2px focus ring in accent, no shadows except dialog-tier. Restraint as character.
- `voices/operate.md § Polish` — layered shadow stack, 1px chroma-tinted borders, 2px focus ring, `--ease-elegant` on success-state row insertions.

## Derivation method

**Named method: distinctive-residual derivation.** Two steps in order: Step 1 is mechanical; Step 2 is the residual.

**Step 1 — Depth system from Spacing (mechanical).**

Derives directly from committed Spacing values. Compact → shadow absent or single boundary-marker tier, 1px hairline borders. Comfortable → 1–2 tiers (`--shadow-sm` / `--shadow-md`). Generous → full stack (`--shadow-sm` / `--shadow-md` / `--shadow-lg`). Dark-first → inset-bg preferred over `box-shadow` (light shadows halate). Keyboard-primary flag → focus ring at 2px minimum; 1px is a functional defect. Accent contrast ≥3:1 → focus ring = accent; below 3:1, fallback = `Highlight`.

Border weight: 1px hairline default. 2px only on focus and selected states.

**Step 2 — Signature detail as residual.**

After all upstream layers commit: "What's the one detail that makes this feel like THIS product, not a generic one?" Two patterns:

*Inherit a peer's signature.* Borrowing is legitimate only when the mood supports it. Linear's focus ring discipline fits any keyboard-primary utilitarian product. Operate's `--ease-elegant` belongs to expressive / craft moods; calm-dense is a register mismatch. Stripe's gradient fits editorial-trustworthy; conflicts with terminal-adjacent.

*Derive own signature from Mood + voices.* When no peer fits: terminal-adjacent → tabular monospace numerals; editorial → drop-cap or pull-quote alignment; refined-warm → hand-drawn divider strokes; calm-dense + monochrome → custom caret on hover-reveals.

**The Rams tension.** § 8 (thorough) and § 10 (less, but better) collide here. Choose one signature detail; commit to it; remove everything else. One detail at full conviction beats three at half-conviction. Default toward removal; don't strip the one detail that gives the product character.

**Anti-patterns:** unlabeled shadow stack; glass/blur used reflexively; custom cursors broken on first paint; decorative noise/grain (visible banding at low resolution). Meta-anti-pattern: skipping Polish — every product makes these decisions whether named or not.

**Worked examples.** Pine IRM ("calm-dense / terminal-adjacent"): 1px hairline borders, `--shadow-sm` on dialog only, 2px focus ring in accent, signature = tabular monospace numerals; Rams tension → removal. Operate ("warm-refined / craft"): three-tier shadow stack, chroma-tinted borders, 2px focus ring, `--ease-elegant` on success states; Rams tension → keeping (elevation hierarchy is load-bearing in a multi-user surface).

## Required output

- `polish:` frontmatter — list of 1–3 specific phrases (e.g. `["1px hairline borders", "2px accent focus ring", "tabular-figures monospace numerals"]`). No vague descriptors.
- `## Polish` prose with two sub-sections: **(a) Depth system** (shadow stack, border weight, focus ring; cite Spacing density + Refactoring UI) and **(b) Signature details** (residual answered; derivation cited; Rams tension resolved).
- Tokens — emit committed tiers only; absent tiers noted with a comment:

```css
--shadow-sm:        /* boundary-marker; dialog/popover tier */
--shadow-md:        /* mid-tier; popover above card */
--shadow-lg:        /* top-tier; modal */
--border-1:         1px solid var(--color-border);
--border-2:         2px solid var(--color-border);
--focus-ring-color: var(--color-accent); /* fallback: Highlight */
--focus-ring-width: 2px;
/* --ease-elegant: cubic-bezier(...); — if signature curve committed */
```

## Dialogue questions

Four questions; skill waits for the user's answer before advancing. Questions 1–3 close the depth system; Question 4 closes the signature residual.

**Question 1 — Shadow stack.**

> "Density = `<compact / comfortable / generous>`. Shadow stack options: (a) light single tier (`--shadow-sm` on boundary-marker surfaces only; `--shadow-md` / `--shadow-lg` intentionally absent); (b) layered tiers (`--shadow-sm` / `--shadow-md` / `--shadow-lg` for card / popover / modal elevation); (c) inset-only (dark-first; depth via border-inset-bg, no shadow). My lean: `<a/b/c>` because `<Spacing density + color scheme>`."

After pick: "Linear lands at (a)/(c) for compact-dark; Operate at (b) for warm-refined; Stripe at (b) + gradient tier on hero moments."

**Question 2 — Focus ring weight + color.**

> "Focus ring: (a) 1px (mouse-primary only); (b) 2px (industry-standard; keyboard-primary default); (c) 3px (bold; low-contrast palettes). Color: accent, or `Highlight` fallback if contrast < 3:1. My lean: 2px in accent. Pick."

If brief flags keyboard-primary: 2px is the floor before presenting options.

**Question 3 — Border weight.**

> "Border weight: (a) 1px hairline throughout; (b) 1px default + 2px on focus and selected states; (c) 2px throughout (conflicts with compact density). My lean: `<a/b>` because `<density + mood>`. Pick."

After pick: "Linear lands at (a); Operate at (a) with chroma-tinted color; Stripe at (a) with a 2px left-border accent on selected card states."

**Question 4 — Signature detail (residual question).**

> "Upstream layers committed: `<mood>`, `<type summary>`, `<color scheme>`, `<density>`, `<motion register>`. Rams § 8: thorough to the last detail. Rams § 10: less, but better. Default toward removal; keep the one detail that gives this product character. Derived from your mood: `<1 mood-specific option>`. Options to inherit: (a) Linear's focus ring discipline; (b) Stripe's gradient accent on hero moments; (c) Operate's `--ease-elegant` overshoot; (d) Rauno-style scroll-snap reveals. Pick or override."

The derived option must be specific to the committed mood — not a generic fallback.

## Constraint surfacing

Surface before Question 1 when the spec flags: **keyboard-primary** (2px focus ring floor); **performance-constrained** (no large-blur `box-shadow`, no `backdrop-filter` — use `border`/`outline`); **high-contrast mode** (`--focus-ring-color` degrades to `Highlight`); **dark-first** (inset-bg preferred below `hsl(220, 8%, 10%)`).

## Options pattern

Derivation-based first throughout. Depth system: name the Spacing connection before any option (density → shadow → border → focus ring). Signature: mood-derived option leads, peer signatures follow. Rams § 8 / § 10 tension named before Question 4 — not buried in prose.

## User-voice prompt

After Question 4:

> "Any polish reference beyond the canonical voices? Drop a URL of a product surface that has a detail you want."

Queue for research at spec-write time — do NOT fetch during dialogue. Voice files land at `<project-root>/docs/visual-design/voices/<slug>.md`. Populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`voices/rauno-micro-details.md`, `canon/refactoring-ui.md § Depth requires intent`, `canon/rams-10-principles.md § 8 + § 10`, `anti-patterns.md § Polish anti-patterns`.

## Gate criteria

- [ ] Depth system committed: shadow stack (tier count + names), border weight, focus ring (weight + color + fallback)
- [ ] ≥1 signature detail named — specific; inherited or mood-derived
- [ ] Rams § 8 / § 10 tension resolved — removal vs. addition stated and justified
- [ ] Tokens emitted: `--shadow-*` (committed subset), `--border-1`, `--border-2`, `--focus-ring-color`, `--focus-ring-width`, signature curves if any
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

Polish captured. Ready to write the visual spec, or should I revise? (yes / revise)
