# Spacing

Pick baseline grid, row-height floor, padding scale via density-rhythm derivation; emit spacing tokens that compose with typography rhythm.

## Canon

- `canon/refactoring-ui.md § Spacing is harder than color` — 8pt grid is the industry standard; padding ≥ margin in all cases. A held grid produces visual coherence that padding-by-feel cannot replicate.
- `canon/lupton-thinking-with-type.md § Grid` — baseline grid pitch must divide evenly into body line-height; otherwise row content floats off the grid and adjacent rows fight each other visually. Lock text to the grid before locking everything else.
- `canon/apple-hig-foundations.md § Layout` — hit-target floors are non-negotiable on touch: 44×44 pt iOS, 44×44 pt iPadOS. Keyboard-primary web relaxes to 24×24 px minimum. Visual size and target size are separate concerns; inset padding bridges them.
- `voices/linear.md § Spacing` — compact density exemplar. 28–32px rows on 8pt grid. Every value a grid multiple. Compactness is the discipline, not the accident.
- `voices/operate.md § Spacing` — refined-warm exemplar, off-standard. 4pt grid, 13.5px base type, 36px row height. Finer granularity earned by the "warm-refined / craft" mood.

## Derivation method

**Named method: density-rhythm derivation.** Three steps, propagated in order. No step may be skipped.

**Step 1 — Mood density → baseline grid.**

| Mood density | Baseline grid |
|---|---|
| compact (terminal-adjacent, dense, utilitarian) | 8pt preferred; 4pt only if type rhythm requires finer steps |
| comfortable (balanced, collaborative) | 8pt — standard |
| generous (editorial, read-mostly) | 8pt; 4pt if type rhythm warrants intermediate steps |

Bias toward 8pt: it is industry-standard (Figma, Material 3, Radix). Off-standard 4pt grids require explicit justification from type rhythm or voice precedent.

**Step 2 — Baseline grid + typography line-height → row-height floor.**

```
line-height px = body font size × line-height ratio
row-height floor = round line-height px up to nearest grid multiple + vertical padding
```

Example: 14px × 1.5 = 21px. Round to 24px on 8pt grid. Add 4px top + 4px bottom → 32px row.

If the brief flags virtualization, row height must be fixed and cannot exceed the virtualization budget (28–36px typical for dense data). Surface this cap before proposing a height.

**Step 3 — Row-height floor + content-density → padding scale.**

| Grid | Scale (px) | Steps |
|---|---|---|
| 8pt standard | 8 / 16 / 24 / 32 / 48 / 64 | 6 |
| 4pt standard | 4 / 8 / 12 / 16 / 24 / 32 / 48 / 64 | 8 |
| 4pt compact | 4 / 8 / 12 / 16 / 24 / 32 | 6 |

Dense data surfaces favor compact scales. Editorial surfaces use broader scales with generous upper steps.

**Anti-patterns:** padding-by-feel (`p-4` here, `p-6` there, no scale relationship); generous padding on dense data (24px cell padding on a 28px row leaves 4px for content); compact padding on read-mostly content; off-grid ornamental values (`margin: 10px`) that don't appear in the scale.

**Worked examples:**

Pine IRM ("terminal-adjacent / dense / calm," virtualization flagged): 8pt grid → 13px × 1.4 = 18.2px → round to 24px + 4px/4px = 32px row → padding scale 4/8/12/16/24/32/48.

Operate ("warm-refined / craft," no virtualization): 4pt grid → 13.5px × 1.5 = 20.25px → round to 24px + 6px/6px = 36px row → padding scale 4/8/12/16/20/24/32/48/64.

## Required output

- `density:` frontmatter field — `<compact | comfortable | generous>; <baseline grid>` (e.g. `compact; 8pt grid`).
- `## Spacing` prose paragraph — mood density signal, grid pick, row-height computation, padding scale, virtualization outcome if present.
- Spacing tokens block — `--space-1` through `--space-N` mapping to the committed scale; emitted at gate close.

## Dialogue questions

Four questions; skill waits for answer before advancing.

**Question 1 — Baseline grid.**

> "Mood density = `<compact / comfortable / generous>`. Grid options: 4pt (finer; off-standard) or 8pt (industry-standard). My lean: `<4pt or 8pt>` because `<derivation rationale>`. Pick."

After pick, cite peers: "Linear lands at 8pt for the same reasons; Operate lands at 4pt, earned by its craft-warm mood and variable face; Stripe runs 8pt with generous upper steps." Never as source.

**Question 2 — Row-height floor.**

> "Type body = `<14px / 13.5px / etc.>` × line-height `<1.4 / 1.5>` = `<computed>`. Round to grid: `<row-height>`. Virtualization flag: `<yes/no>`. If yes, floor cannot exceed `<floor>` per virtualization budget. My lean: row = `<height>px`. Pick."

After pick: "Linear lands at 28–32px; Operate at 36px; Stripe at 48–56px for its editorial register."

**Question 3 — Padding scale.**

> "Padding scale options: 4/8/12/16/24/32/48/64 (4pt grid, 8 steps); 8/16/24/32/48/64 (8pt grid, 6 steps); 4/8/16/24/32 (compact, 5 steps). My lean: `<scale>`. Pick."

After pick, remind: every spacing value in the handoff brief must reference a named token from this scale — no ad-hoc px values.

**Question 4 — Hit-target floor.**

> "Hit-target floor: 44×44 pt iOS / touch surfaces; 24×24 px keyboard-only web (Apple HIG). Verify smallest interactive element ≥ floor. If smaller, options: (a) increase touch padding via inset; (b) accept keyboard-only with explicit acknowledgment. Pick."

Inset padding — making the touch target larger than the visual element — is the preferred path for touch surfaces. Option (b) is valid only for confirmed keyboard-primary desktop products.

## Constraint surfacing

Surface before Question 1 when the brief flags:

- **Virtualization.** Row height must be fixed (variable height kills virtualization performance). Cap = 28–36px typical.
- **Keyboard-primary surfaces.** Hit-target relaxes to 24×24 px. Tighter row height is valid; minimal row padding is acceptable.
- **Touch-primary surfaces.** Minimum = 44×44 pt iOS / 48×48 dp Material. Row height must accommodate the target even if visual content is shorter. Inset padding is the mechanism.
- **Multilingual.** German and inflected strings run 20–35% longer than English. Tight horizontal padding may clip translated labels; surface this before the user commits to the scale's low end.

## Options pattern

Derivation-based first. Step 1 → Step 2 → Step 3 chain must appear before any peer reference. Lead with "my lean is X because mood density / type rhythm / brief flag." After each commit, cite peers as convention precedent in one sentence. Two alternates per question: one step more compact, one step more generous. "Override" is always a third path.

## User-voice prompt

After peer comparison following Question 1:

> "Any spacing reference beyond the canonical voices? Drop a name, framework, or design-system URL."

Captured references queue for research at brief-write time — do NOT fetch during dialogue. Voice files land at `<project-root>/docs/visual-design/voices/<slug>.md`. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the brief.

## Principles invoked

`canon/refactoring-ui.md § Spacing is harder than color`, `canon/lupton-thinking-with-type.md § Grid`, `canon/apple-hig-foundations.md § Layout`, `voices/linear.md § Spacing`, `voices/operate.md § Spacing`, `anti-patterns.md § Spacing anti-patterns`.

## Gate criteria

- [ ] Baseline grid committed (4pt or 8pt; in `density:` frontmatter)
- [ ] Row-height floor committed (pixel value; virtualization cap applied if flagged)
- [ ] Padding scale committed (named scale steps; all values on-grid)
- [ ] Hit-target floor verified (touch ≥ 44×44 pt; keyboard-only ≥ 24×24 px; inset strategy named if needed)
- [ ] Tokens emitted (`--space-1` through `--space-N` mapping to committed scale)

## Transition prompt

Spacing captured. Ready to move to Motion, or should I revise? (yes / revise)
