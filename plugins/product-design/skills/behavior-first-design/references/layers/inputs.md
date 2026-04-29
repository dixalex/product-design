# Inputs

Pick per-surface input modality priority via modality-priority matrix; emit `inputs:` frontmatter array.

## Canon

- `canon/apple-hig-foundations.md § Inputs` — HIG uniquely elevates Inputs to a top-level peer alongside layout and color; modality is upstream of every later layer, pinning chord maps, hit-target floors, and motion budgets downstream.
- `foundations.md § Fitts's Law` — pointer surfaces lower hit-target floors because cursor precision is high; touch surfaces raise them to 44×44 pt minimum because finger contact area is large and imprecise.
- `foundations.md § Hick's Law` — modality choice affects choice complexity: touch surfaces afford fewer chord combinations and simpler input vocabularies; keyboard surfaces license richer chord sets and therefore more complex action menus.
- Nielsen heuristic #7 (Flexibility and efficiency of use) — power users license keyboard chords and accelerators; novice users license pointer-primary interaction; the surface's persona determines which license is granted.

## Derivation method

**Named method: modality priority matrix.**

For each surface in the structural spec, apply these two reads in order, then emit a row:

**Read 1 — JTBD verbs.** Extract the dominant action verbs from the JTBD statement for that surface. High-frequency action verbs ("triage", "send", "log", "assign") signal keyboard-primary: the user is operating at speed and needs hands on keys. Reading or orientation verbs ("review", "explore", "compare", "browse") signal pointer-primary: the user is scanning and selecting, not executing a stream of commands.

**Read 2 — Persona tool-adjacency.** Check which tool context the persona lives in day-to-day. A keyboard-first solo founder whose adjacent tools are terminal and editor licenses keyboard-primary. A click-primary SMB team whose adjacent tools are spreadsheets and web forms licenses pointer-primary. A mobile-first user whose adjacent context is a phone licenses touch-primary.

**Output per surface:** `{surface, primary_modality, secondary_modality, declined_modalities}`.

**Worked example — Pine IRM inbox:**

- JTBD: "When working through morning leads, I want to triage faster than my caffeine wears off."
- Persona: solo founder, keyboard-first; adjacent tools are terminal and editor.
- Applying the reads:
  - JTBD verb "triage" at speed → keyboard-primary licensed.
  - Tool-adjacency: keyboard-first solo founder → keyboard-primary confirmed.
- Result: `{surface: inbox, primary: keyboard, secondary: pointer, declined: touch}`

**Anti-pattern: blanket pointer-primary.** Defaulting to pointer-primary across all surfaces because "we have a desktop product" skips the derivation. On a surface whose JTBD is speed-triage and whose persona is keyboard-first, pointer-primary conflicts with cycle-time goals — every action that requires a mouse click adds a context switch the power user must absorb. The matrix forces surface-by-surface justification before the default is applied.

## Required output

The Inputs layer populates two artifacts in the handoff spec:

- `inputs:` frontmatter array — one entry per structural-spec surface, each entry containing `surface`, `primary_modality`, `secondary_modality`, and `declined_modalities`.
- `## Inputs` prose paragraph in the spec body — explains the per-surface choices, citing the JTBD verbs and persona signals that drove each decision.

## Dialogue questions

The Inputs layer runs exactly two questions. The skill waits for the user's answer before advancing.

**Question 1 — Derivation and per-surface lean.**

The skill auto-extracts modality candidates per surface from the structural spec and proposes a per-surface lean. Prompt shape:

> "Reading your structural spec: surfaces `<list>`, JTBD verbs `<quote>`, persona `<quote>`. Per-surface modality lean: `<surface 1>: keyboard-primary` (because <rationale citing JTBD verbs and persona tool-adjacency>); `<surface 2>: pointer-primary` (because <rationale>). Which lands, or override per-surface?"

Expected answer shape: a confirmation per surface, or a list of surface-level overrides.

The skill must quote JTBD verbs and persona signals directly from the structural spec — proposing a lean without citing the spec is peer-mimicry in disguise. After the user confirms, cite peer precedent: "Linear lands keyboard-primary throughout for a similar persona; Operate adds pointer-primary on detail panels for non-AE users." Precedent follows confirmation; it does not precede derivation.

**Question 2 — Declined modalities confirmation.**

> "Touch declined for v1 across all surfaces? (yes / no — if no, which surfaces support touch?)"

Expected answer shape: yes, or a list of surfaces that carry touch-primary or touch-secondary.

Purpose: forces explicit exclusion so downstream layers (hit-target floors in Layer 2, motion floors in Layer 6) know which constraints are live.

## Constraint surfacing

Before Question 1, the skill checks the structural spec for performance and input flags:

- If the structural spec flags **virtualization** (e.g., a 100k-row list), keyboard-primary on that surface inherits a 0ms motion floor downstream — animations tied to keyboard navigation cannot run while the virtual scroll is repositioning. Surface this before the user commits.
- If the structural spec flags **touch-primary** on any surface, hit-target floors on that surface jump to 44×44 pt, rippling into Layer 2 (keyboard and focus) and Layer 6 (motion). Surface this so the user understands the downstream cost before confirming touch.

Surface constraints before Question 1 so they are already in scope when the user evaluates the per-surface lean.

## Options pattern

Always derivation-based first. The skill leads with "my lean is X because…" and cites the specific JTBD verbs and persona signals that produced the lean before any peer reference appears.

After the user confirms, the skill cites peer precedent as evidence — not as source:

- Linear lands keyboard-primary throughout; JTBD and persona alignment for a keyboard-first solo tool.
- Operate adds pointer-primary on detail panels for non-AE users who are reviewing rather than executing.

The framing is always "Linear lands keyboard-primary for similar reasons" — not "pick something like Linear." Derivation stays primary; precedent is corroboration.

## User-voice prompt

At the end of the Inputs layer, after Question 2, the skill asks:

> "Any input-modality reference beyond canonical voices? Drop a name."

Captured references queue for research — do NOT fetch during dialogue. User-supplied references populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`canon/apple-hig-foundations.md § Inputs`, `foundations.md § Fitts's Law`, `foundations.md § Hick's Law`, Nielsen #7 (Flexibility and efficiency of use).

## Gate criteria

- [ ] Per-surface `inputs:` array populated (at least one row per structural-spec surface, each with primary, secondary, and declined modalities)
- [ ] Declined modalities confirmed (touch explicitly confirmed in or out for v1)
- [ ] Constraints surfaced (if the structural spec flagged virtualization or touch-primary on any surface)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

Inputs captured. (Layer 1 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.)
Ready to move to Focus & Keyboard, or should I revise? (yes / revise)
