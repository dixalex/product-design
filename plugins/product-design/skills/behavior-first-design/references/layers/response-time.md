# Response-time & Optimism

Set response budget per action; mark which mutations take optimistic UI; emit `response_time:` frontmatter.

## Canon

- `response-time-budget.md` — full canon on per-action budget tiers and optimistic-UI contracts; the primary reference for this layer.
- Nielsen, *Response Times: The 3 Important Limits* (1993) — 100ms perceptual continuity / 1s flow / 10s attention limit.
- Doherty Threshold (1982) — 400ms productivity boundary; below it the user and system stay in a tight feedback loop.
- Linear's optimistic-UI conventions (`voices/linear.md`) — most mutations commit optimistically with a short rollback window; the pattern is the canonical reference for Pine IRM-style products where speed-of-interaction is itself a product value.

## Derivation method

**Named method: response-budget table.**

For each action in the structural spec's Flows, run three steps in order before proposing a budget tier or optimistic flag.

**Step 1 — Classify the action by tier.** Match the action to one of five tiers:

| Tier | Target | Signal |
|---|---|---|
| Instant | 0ms perceived | Hover reveals, virtualized row interactions, inline selection state |
| <100ms | Perceptual continuity | Read-only UI transitions, focus changes, toggle states |
| <400ms | Doherty productivity | Most mutations with fast server round-trips |
| <1s | Flow | Mutations with moderate server cost; ID-generating actions |
| <10s | Attention limit | Heavy operations; requires visible progress indicator |

**Step 2 — Apply optimistic UI test.** For every mutation, check two conditions: (1) Is the action reversible? (2) Is the server round-trip expected under 2 seconds? Both yes → optimistic; show toast-undo; rollback on failure. Either no → not optimistic; show inline saving spinner.

**Step 3 — ID-generating actions.** Any action that creates a server-assigned ID is never optimistic regardless of reversibility or round-trip estimate. The client cannot safely reference the record until the server returns the canonical ID.

**Worked examples — Pine IRM:**

- *mark-task-done*: mutation / reversible / <400ms round-trip → optimistic + toast-undo. Classification: Doherty tier.
- *new-lead-create*: mutation / ID-server-generated / <1s round-trip → not optimistic; show inline saving spinner while server returns ID. Classification: <1s flow tier.

**Anti-pattern: optimistic UI on irreversible actions.** Hard-delete with no soft-delete backing: the UI reverts visually but the record is gone. Rollback cannot undo it. Never mark an irreversible action optimistic.

## Required output

The Response-time & Optimism layer populates two artifacts in the handoff spec:

- `response_time:` frontmatter array — one entry per action from the structural spec's Flows. Each entry contains: `action` (name), `tier` (instant / <100ms / <400ms / <1s / <10s), `optimistic` (yes / no), and `rationale` (one phrase citing the Nielsen or Doherty threshold that places it in that tier).
- `## Response-time & Optimism` body section — budget table (one row per action, columns: action / tier / optimistic / rollback-method), optimistic-UI inventory (list of all optimistic mutations with their toast and rollback behavior), and a rationale paragraph per Nielsen tier citing which actions land in it and why.

## Dialogue questions

The Response-time & Optimism layer runs exactly three questions. The skill waits for the user's answer before advancing.

**Question 1 — Per-action budget tier.**

The skill auto-extracts actions from the structural spec's Flows and proposes a tier per action. Prompt shape:

> "Action `<name>`: read-only / mutation / ID-gen. My lean: <ms tier> (because <1-line derivation citing action class and Nielsen/Doherty threshold>). Pick."

One action at a time, or batched by tier if many actions share a tier. The skill must cite the specific threshold — proposing a tier without derivation is peer-mimicry. After confirmation, cite peer precedent: "Linear targets <400ms on most mutations; Operate adds <1s for heavier pipeline actions."

**Question 2 — Optimistic UI per mutation.**

For each mutation confirmed in Question 1, the skill applies the two-condition test and proposes a flag. Prompt shape:

> "Mutation `<name>`: reversible? <yes/no>. Round-trip <2s? <yes/no>. My lean: optimistic / not-optimistic. Pick."

After confirmation, describe rollback: "On failure, revert state and surface an inline error; toast-undo window is 5s by Linear convention." ID-generating actions are flagged not-optimistic automatically — hard constraint, not a preference.

**Question 3 — Reduced-motion accessibility.**

> "Reduced-motion users (`prefers-reduced-motion: reduce`) hit the same response-time targets but animations are replaced with instant state changes and spinners. Optimistic toasts swap from slide-in to fade-in. No behavior changes — only motion changes. Acknowledged?"

Expected: yes, or a request to handle a specific surface differently.

## Constraint surfacing

Surface these constraints before Question 1 when the structural spec flags them:

- **Virtualized lists.** Virtualization caps perceived latency on row interactions to instant (0ms tier). No per-row animation may run while the virtual scroll is repositioning; downstream Layer 6 (Motion) inherits a 0ms floor on those interactions.
- **High-frequency hover-reveals.** Any UI element that fires on cursor position (tooltip, row action bar, inline preview) must lock to 0ms reveal delay; a debounce or animation delay creates visible lag that accumulates across a triage session.

Surface these before Question 1 so the derivation is already scoped when tier proposals begin.

## Options pattern

Derivation-based first. The skill leads with "my lean is X because…" and cites the specific action class, Nielsen tier, and reversibility/round-trip conditions that produced the lean before any peer reference appears.

After the user confirms, cite peer precedent as evidence — not source:

- "Linear's optimistic conventions: most mutations optimistic with a 5s rollback window; Operate adds explicit confirm for high-stakes actions (irreversible pipeline changes) rather than making them optimistic."

Derivation stays primary; precedent is corroboration.

## User-voice prompt

At the end of the Response-time & Optimism layer, after Question 3:

> "Any response-time reference beyond canon? Drop a name or framework."

Captured references queue for research — do NOT fetch during dialogue. Populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

`response-time-budget.md`, Nielsen *Response Times: The 3 Important Limits* (1993), Doherty Threshold (1982), `voices/linear.md` (optimistic-UI conventions per plugin canon).

## Gate criteria

- [ ] Every action in the structural spec's Flows has a budget row (tier + optimistic flag populated)
- [ ] Optimistic UI flagged per mutation (yes / no, with rollback method noted for all optimistic mutations)
- [ ] Reduced-motion accessibility row acknowledged (Question 3 confirmed)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

```
Response-time & Optimism captured. (Layer 3 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.)
Ready to move to Feedback & Recovery, or should I revise? (yes / revise)
```

## Mid-session checkpoint

After Layer 3 (Response-time & Optimism), the skill emits an opt-in pause prompt — parallel to visual-design's Color-layer checkpoint. Three layers deep is when 6-layer fatigue actually starts; offering pause here mirrors visual-design's discipline.

Verbatim checkpoint prompt (emit AFTER the Transition prompt above):

> Behavior spec in progress saved to <project-root>/docs/behavior-first-design/<date>-<slug>.md (incomplete). Resume now with Feedback & Recovery → Motion fire-policy → Component binding, or pause and continue in a new session?

If user pauses: skill writes `# Behavior spec — IN PROGRESS` marker at top of spec file. On resume, the skill reads the in-progress spec, confirms committed layers (Inputs, Focus & Keyboard, Response-time & Optimism), and continues at Feedback & Recovery without re-running earlier questions.
