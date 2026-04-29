# Motion fire-policy

Decide what animates per surface using the frequency-novelty matrix; values come from visual-design's tokens; emit `motion_fire_policy:` frontmatter.

## Canon

- `motion.md` — frequency-novelty matrix and fire-policy authority; the primary reference for this layer. Owns the policy: when motion fires, when it doesn't, and why.
- `visual-design/references/tokens/motion-defaults.md` — duration and easing values (read-only here; visual-design owns these values). This layer reads them at startup; it never invents them.
- `voices/rauno-micro-details.md` (visual-design plugin canon) — motion as communication; the source of the frequency-novelty matrix and the boundary-marker carve-out for popovers and tooltips.
- `canon/apple-hig-foundations.md § Motion` — HIG motion vocabulary; cited for context and contrast; Rauno/Linear doctrine supersedes HIG for web CRM surfaces (see `motion.md § Conflict with HIG`).
- `anti-patterns.md § Motion anti-patterns` — no spring or bounce in product UI; no animation on every action.

## Derivation method

**Named method: frequency-novelty matrix.**

For each action class in the structural spec's Flows, apply two reads in order, then assign a fire-policy cell.

**Read 1 — Action frequency.** How many times per day does this user class perform this action? Row-select, hover-reveal, and keyboard nav blow past 50/day in a triage-heavy tool. Assign `frequent` (≥50/day) or `rare` (<50/day).

**Read 2 — Action novelty.** Has the user seen this interaction a hundred times before? Row-select is zero-novelty after the first session. A dialog open marks a scope boundary — even at high frequency, each open is a boundary crossing, treated as spatially novel. Assign `low-novelty` or `high-novelty`.

**Three output cells:**

| Cell | Frequency + Novelty | Fire policy | Rationale |
|---|---|---|---|
| 1 | Frequent + low-novelty | **0ms instant** | Repeated interactions become cognitive overhead at non-zero duration; stillness is faster |
| 2 | Rare + high-novelty | **150–200ms ease-out** | Boundary markers earn motion; duration from `motion-defaults.md`; helps orient the user |
| 3 | Boundary-marker exception | **150ms despite frequency** | Rauno carve-out: popovers and tooltips mark a UI boundary regardless of frequency |

**Worked examples — Pine IRM:**

- *Hover chord-chip on row*: frequent (100+/day) + low-novelty → Cell 1 → **0ms instant**. The chip appears the moment the cursor enters the row target; any delay accumulates across a triage session.
- *Dialog enter*: rare (<20/day) + high-novelty → Cell 2 → **150–200ms ease-out** per `motion-defaults.md § dialog enter`. The enter communicates the boundary crossing; the duration value comes from visual-design's token, not from this layer.
- *Tooltip on hover-icon*: frequent (100+/day) + would normally be Cell 1, but triggers boundary-marker exception → Cell 3 → **150ms** despite high frequency. Rauno's carve-out holds: the tooltip is a UI boundary, not a repetitive state swap.

**Crucial separation — fire-policy vs. value selection.** This layer decides *what animates* and *what stays instant*. It does not pick duration values. All non-zero durations reference `visual-design/references/tokens/motion-defaults.md` by name. If a project's visual-design brief changes a token value, this layer's output does not change — the fire-policy remains correct; the token updates downstream automatically.

**Anti-patterns:**

- *Spring/bounce in product UI.* Consumer-app vocabulary (Dynamic Island, iMessage stickers). Actively hostile in a tool opened at 9am and closed at 6pm. Hard rule from `motion.md § Easing` and `anti-patterns.md`.
- *Animating on every action.* Motion fatigue defeats the productivity gains from Layer 3 (Doherty Threshold). Every non-zero duration is a tax; collect it only where the boundary-marking benefit exceeds the cognitive cost.

## Required output

Two artifacts in the handoff spec:

- `motion_fire_policy:` frontmatter — three sub-keys: `frequent_low_novelty` (instant, 0ms), `rare_high_novelty` (animate; token name from `motion-defaults.md`), `boundary_marker_exception` (animate-despite-frequency; token name). Each sub-key lists the actions assigned to it.
- `## Motion fire-policy` body section — matrix output table (action / cell / fire-policy / token reference) + reduced-motion contract acknowledgment.

## Dialogue questions

Two questions. The skill waits for each answer before advancing.

**Question 1 — Per-action fire-policy confirmation.**

The skill auto-extracts action classes from the Flows, applies the matrix, and proposes a cell per action:

> "Action class `<frequent+low-novelty | rare+high-novelty | boundary-marker>`: My lean: `<instant / animate / animate-despite-frequency>`. Duration token: `<motion-defaults.md § key>` (or 0ms). Pick or override."

One action class at a time, or batched by cell when several actions share the same assignment. After confirmation, cite peer precedent: "Linear lands instant (0ms) on row-select and keyboard nav for the same frequency reasons; boundary markers (dialog, peek) get short enter animations consistent with Cell 2."

**Question 2 — Reduced-motion contract acknowledgment.**

> "Reduced-motion contract: `prefers-reduced-motion: reduce` collapses all non-zero durations to 0.01ms; spatial transforms become opacity-only fades. Wired in at authoring time — not retrofitted. 0.01ms (not true zero) preserves `transitionend` firing. Acknowledged?"

Compliance gate, not a preference. Vestibular disorders and migraine triggers make this unconditional.

## Constraint surfacing

Before Question 1, surface carry-forward floors from prior layers:

- **Virtualized lists (Layer 1 + Layer 3 floor).** Virtualization locks row interactions to 0ms; no per-row animation may run while the virtual scroll repositions. Surface these as committed floors — not new decisions — before the matrix walk begins.
- **Keyboard-primary surfaces (Layer 1 floor).** Keyboard nav and row-select are frequent + low-novelty by construction on any keyboard-primary surface; they always resolve to Cell 1. Confirm the floor before advancing.

## Options pattern

Derivation-based first. The skill cites the frequency read and novelty read that produced each cell assignment before any peer reference appears. After confirmation: "Linear lands here for the same calm-restraint reasons. Visual-design's expressive moods license signature curves at Polish-tier — but those are Polish, not Motion." Derivation stays primary; precedent is corroboration.

## User-voice prompt

At the end of the Motion fire-policy layer, after Question 2:

> "Any motion-fire reference beyond canon? Drop a name."

Queue for research — do NOT fetch during dialogue. Populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`motion.md`, `visual-design/references/tokens/motion-defaults.md` (read-only), `voices/rauno-micro-details.md` (visual-design canon; boundary-marker carve-out), `canon/apple-hig-foundations.md § Motion`, `anti-patterns.md § Motion anti-patterns`.

## Gate criteria

- [ ] Per-surface or per-action fire-policy committed (all three cells answered: `frequent_low_novelty` / `rare_high_novelty` / `boundary_marker_exception` each have an action list)
- [ ] Reduced-motion contract acknowledged (0.01ms collapse wired in at authoring time)
- [ ] No invented duration values (every non-zero duration cites `motion-defaults.md` by token key)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

```
Motion fire-policy captured. (Layer 5 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.)
Ready to move to Component binding, or should I revise? (yes / revise)
```
