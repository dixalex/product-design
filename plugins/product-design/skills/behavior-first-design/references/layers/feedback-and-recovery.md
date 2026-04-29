# Feedback & Recovery

Decide status signals, error patterns, undo/confirm policy per action; emit `feedback:` frontmatter.

## Canon

- Nielsen #1 (Visibility of system status) — users must always know what the system is doing; status signals are the primary trust contract.
- Nielsen #3 (User control and freedom) — every reversible action needs an escape hatch; absence of undo is a design failure, not a scope decision.
- Nielsen #5 (Error prevention) — the best error message is one the user never sees; validation timing is an error-prevention lever.
- Nielsen #9 (Help users recover from errors) — error messages must name the problem and offer a path out; a red border with no label violates this heuristic.
- `anti-patterns.md` — toast-fatigue and confirm-fatigue patterns; the anti-pattern canon for this layer.
- Polaris Patterns — toast+undo, inline validation, confirm dialogs; peer convention that documents the trade-offs this layer resolves.
- `foundations.md § Peak-End Rule` — recovery moments are end-anchors of episodic memory; a botched undo or a dismissive error message is the note the user walks away remembering.

## Derivation method

**Named method: status-feedback decision tree.**

Run the tree once per action from the structural spec's Flows. Four cases, mutually exclusive.

**Case 1 — Reversible mutation.** Signal: toast+undo with a 5-second countdown. The toast names the action and offers a single Undo affordance. On 5s expiry: commit. Reserve for actions that can actually be undone.

**Case 2 — Irreversible mutation.** Signal: confirm dialog with explicit Cancel (Esc closes, identical effect). Dialog copy names what will be lost — not just "Are you sure?" Anti-pattern: confirm-on-everything desensitizes users; reserve confirm for irreversible actions only.

**Case 3 — Long-running mutation.** Round-trip over 1 second (per Layer 3 budget). Signal: inline spinner on the triggering element for operations under 10 seconds; modal progress bar for longer. Never leave the user with a frozen UI.

**Case 4 — Validation failure.** A prevention signal, not a mutation signal. Timing drives friction:
- On-blur: default for low-stakes fields (name, notes, tags). Fires after the user leaves the field.
- On-change: high-stakes fields where the value affects downstream calculations in real time (probability%, prices, dates feeding pipeline math).
- On-submit: batch forms and multi-step flows where values only matter in aggregate.

**Worked examples — Pine IRM:**

- *mark-task-done*: reversible / <400ms round-trip (per Layer 3) → Case 1 — toast+undo 5s. Copy: "Task marked done. Undo."
- *archive-lead*: irreversible (record leaves active view permanently) → Case 2 — confirm dialog. Copy: "Archive [Lead Name]? This removes the lead from all active views. Archive / Cancel."
- *probability% field*: on-change validation — probability feeds pipeline math; a stale bad value corrupts the total silently.
- *lead-name field*: on-blur validation — low-stakes text; keystroke-level interruption adds friction with no prevention benefit.

Anti-pattern: **toast-on-everything.** Toast for irreversible actions leaves no recovery path once the 5s window closes. Reserve toast+undo for reversible actions where undo is the actual recovery affordance.

## Required output

The Feedback & Recovery layer populates two artifacts in the handoff spec:

- `feedback:` frontmatter array — one row per action. Each row: `action`, `signal` (toast+undo / confirm / progress / none), `recovery` (undo / cancel+esc / rollback / n/a), `validation_timing` (on-blur / on-change / on-submit / n/a).
- `## Feedback & Recovery` body section — decision-tree results as a table (action / signal / recovery / validation timing), rationale paragraph citing anti-patterns avoided, and accessibility checklist: ARIA live regions on status changes (`polite` for toasts, `assertive` for errors); focus moved to first error field on validation fail; confirm dialogs implement focus trap; Undo affordance reachable by keyboard.

## Dialogue questions

The Feedback & Recovery layer runs exactly three questions. The skill waits for the user's answer before advancing.

**Question 1 — Reversibility per mutation.**

The skill walks each mutation from the Flows and proposes a signal based on the decision tree. Prompt shape:

> "Action `<name>`: reversible? My lean: `<toast+undo / confirm / progress>` (because `<1-line derivation citing reversibility + Layer 3 round-trip tier>`). Pick."

One action at a time, or batched by case. After confirmation, cite peer precedent: "Linear lands toast+undo for most mutations; confirm only on team-deletion; Polaris Patterns documents this trade-off explicitly."

**Question 2 — Validation timing per form/field.**

The skill proposes timing per form and per high-stakes field. Prompt shape:

> "Form/field `<name>`: on-blur / on-change / on-submit. High-stakes? My lean: `<timing>` (because `<1-line derivation citing downstream consequence>`). Pick."

**Question 3 — Accessibility acknowledgment.**

> "Feedback & Recovery requires: ARIA live regions on status changes (polite for toasts, assertive for errors); focus moved to first error field on validation fail; confirm dialogs implement focus trap (Esc closes, focus returns to trigger); Undo affordance reachable by keyboard. Acknowledged?"

This is a compliance gate — ARIA live regions and focus-on-error are not optional.

## Constraint surfacing

Before Question 1, surface carry-forward constraints from prior layers:

- **Low-saturation accent color (from visual spec).** If the visual-design layer used a near-monochrome palette, color alone cannot differentiate status states. Status signals must combine icon + label + color — never color-alone. Surface before the user commits signal designs.
- **Optimistic mutations from Layer 3.** Every action flagged optimistic in `response_time:` frontmatter generates a toast+undo automatically. Confirm the list matches Layer 3 commitments before adding new signals.

## Options pattern

Derivation-based first. The skill leads with "my lean is X because…" and cites the reversibility determination and Layer 3 round-trip tier before any peer reference appears.

After the user confirms, cite peer precedent as evidence — not source: "Linear lands toast+undo for most mutations with a 5s rollback window; confirm only on team-deletion. Polaris Patterns documents this as an explicit trade-off resolved by reversibility." Derivation stays primary.

## User-voice prompt

At the end of the Feedback & Recovery layer, after Question 3:

> "Any feedback/recovery reference beyond canon? Drop a name."

Captured references queue for research — do NOT fetch during dialogue. Populate `user_voices:` frontmatter and `## User-cited voices` in the spec.

## Principles invoked

Nielsen #1/#3/#5/#9; `anti-patterns.md` (toast-fatigue, confirm-fatigue); `foundations.md § Peak-End Rule` (recovery moments are end-anchors of episodic memory).

## Gate criteria

- [ ] Every mutation has a recovery row (signal + recovery mechanism committed; no mutation left unassigned)
- [ ] Validation timing committed per form/field (on-blur / on-change / on-submit explicit for every form surface)
- [ ] Accessibility: ARIA live regions + focus-on-error confirmed (Question 3 acknowledged; checklist in spec body)
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

```
Feedback & Recovery captured. (Layer 4 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.)
Ready to move to Motion fire-policy, or should I revise? (yes / revise)
```
