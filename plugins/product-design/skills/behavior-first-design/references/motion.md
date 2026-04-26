# Motion doctrine

## Core rule: motion is semantic, never decorative

Motion MUST communicate a state change or spatial relationship. If it doesn't, remove it.

Every animation in a product UI answers one of two questions: *what just happened?* (state change) or *where did this thing come from / go to?* (spatial relationship). If an animation answers neither, it's decoration — and decoration in a high-frequency work tool is friction dressed up as polish. Rauno Freiberg's observation, built from his own bookmarking-tool experiment, is the reference case: he intuitively added animation to list add/remove and the active indicator, felt great about it for a day, and within 48 hours the same motion began to "feel sluggish" — even after he made it snappier. Removing motion from the core keyboard-driven interactions made him feel like he was moving "much faster." The motion wasn't slow; it was cognitively expensive on repeat.

This is the doctrine. Semantic motion earns its runtime. Everything else is tax.

## Frequency-novelty matrix (Rauno)

| Action frequency | Action novelty | Motion rule |
|---|---|---|
| High (≥50/day) | Low (repeated) | **NO motion** — even "delightful" animation becomes cognitive burden |
| High | High (first time) | Minimal motion; remove after first N uses |
| Low | High | Motion OK — helps onboarding |
| Low | Low | Minimal or no motion |

The matrix is the core operational tool. It turns "does this deserve animation?" from a taste question into a lookup. Ask two things: *how often does this user do this in a day?* and *has this user seen this a hundred times before?* The answers drop you into one of four cells, and the cell dictates the rule. (The ≥50/day floor is conservative — in a working CRM, row-select, keyboard-nav, and hover-reveal blow past it by an order of magnitude. Rauno's original framing uses "repeated" without a number; we set a floor so the rule is testable.)

Rauno's corroborating examples from macOS are instructive: right-click menus appear with no motion (thousands of invocations per day, zero novelty); the macOS App Switcher overlay never animates (heavy keyboard use, snappy between apps); when the Command–Tab delta is small enough, the menu doesn't even render — the OS just swaps focus. These are not omissions. They are motion designers *choosing* stillness because stillness is faster when the user already knows what's about to happen.

Examples:
- Row selection: high freq, low novelty → NO motion
- Keyboard nav: high freq, low novelty → NO motion
- Click feedback: high freq, low novelty → NO motion (instant state swap)
- Peek open/close: low freq, high novelty → animate (boundary-marker timing per `visual-design/references/tokens/motion-defaults.md § peek/drawer enter`)
- New row entry: low freq, high novelty → animate (per `visual-design/references/tokens/motion-defaults.md § new row slide-in`)
- Dialog open: medium freq, low novelty → short animate (per `visual-design/references/tokens/motion-defaults.md § dialog enter`)

**Why dialogs get motion despite frequency.** Dialogs straddle the matrix: they may be opened many times per session (high-frequency by count), but each open marks a significant state boundary (scope change, modal context). For boundary-marking interactions, treat "novelty" as spatial, not temporal — a dialog's fast enter (see `visual-design/references/tokens/motion-defaults.md § dialog enter`) animates the *boundary crossing*, not the frequency. The matrix governs most cases; boundary markers (dialogs, peeks, drawers) earn motion even at high frequency.

## Duration defaults

Concrete duration values live in `visual-design/references/tokens/motion-defaults.md` (migrated at v0.3.0). This file owns the *policy* — when motion fires, when it doesn't, and why; the values themselves are owned by visual-design's Motion layer so they compose with the project's chosen aesthetic.

The categories the policy distinguishes (the values are filled by visual-design's brief at use-time):

- Dialog / popover / tooltip enter — small-surface boundary marker
- Peek / drawer enter; new content entry — spatial traversal
- New row slide-in — spatial translation
- Toast enter / auto-dismiss — boundary marker + attention-hold
- Keyboard nav / row select / button press / hover reveal — instant; 0 by policy

**On the ordering:** durations track *perceptual effort*, not surface size. A row slide-in is a spatial translation — the eye actually tracks motion through space. A dialog fade-in is an opacity change on an established canvas — no tracking required. Peek/drawer enter combines both translation and contextual repositioning. Row slide > dialog fade; peek > row slide. When in doubt, shorter wins. You can always slow a motion that feels abrupt; you cannot speed up one that users have already internalized as sluggish. In-house values are in `visual-design/references/tokens/motion-defaults.md`.

The instant tier is not a degenerate case — it's a first-class policy default. Instant state swaps (`row.selected = true`) communicate more clearly than any non-zero duration, because the user's attention is already on the target of the interaction.

## Easing

- `ease-out` (decelerate) for enters
- `ease-in` (accelerate) for exits
- **Never** bounce or spring in product UI (Rauno/Linear violation)
- **Never** longer than 300ms in product context

Enters decelerate because the element is arriving — it settles into place, matching the real-world physics of an object landing. Exits accelerate because the element is leaving — it should get out of the way. Fluent 2 endorses both as the natural defaults ("ease-out animations start fast and then slow down" for arrivals; the reverse for departures) and reserves linear easing for cases that genuinely need a constant rate, like indeterminate rotations.

Bounce and spring are specifically out of scope for CRM / productivity UI. They are at home in playful consumer contexts (iMessage stickers, Dynamic Island morphs); they are actively hostile in a tool a user opens at 9am and closes at 6pm. Linear's 2026 refresh makes this explicit, and Rauno's "Kinetic Physics" section draws the line by analogy: a playing card swipe has less bounce than a phone unlock because the card is "conceptually lighter and does not magnetically morph into something." Work tools should feel like playing cards, not like Dynamic Island.

The 300ms ceiling is not arbitrary. Anything longer than roughly a third of a second starts to feel deliberate — the user notices they are waiting. For state changes that don't justify the user's attention (most of them), 300ms is already too long.

## `prefers-reduced-motion` (reduced motion)

ALWAYS respected. When set, durations → 0 (with opacity/visibility swap for state clarity). This is unconditional — there is no motion effect so important that it overrides the OS-level accessibility preference. Vestibular disorders, migraine triggers, and attention sensitivities are non-negotiable design constraints.

```css
@media (prefers-reduced-motion: reduce) {
  * { transition-duration: 0.01ms !important; animation-duration: 0.01ms !important; }
}
```

The 0.01ms value (rather than a true zero) is deliberate: it preserves `transitionend` event firing, so any JS that depends on animation completion still runs. State continuity is maintained through opacity/visibility swaps rather than spatial animation, which Fluent 2's accessibility guidance aligns with: "Keep the motion constrained to the element in focus" and "Consider other ways of communicating information that conveyed via animations, like using ARIA live regions."

See `response-time-budget.md § Numeric targets` for how reduced-motion interacts with interruptible transitions.

## Conflict with HIG

HIG motion permits richer motion vocabularies for state continuity (card-flip, hero transitions). Rauno/Linear prescribe restraint. For web CRM / Linear-style apps: **Rauno wins**. See `_decisions-ledger.md § 2026-04-17 item 6`.

The rationale is context, not disagreement. Apple's HIG is written for touch-first, icon-driven, lower-frequency-per-surface interactions: opening an app a few times per session, flipping between cards, presenting hero content. Rich motion is semantic there — it tells you where content came from spatially, across a canvas with lots of room to move. A CRM is the opposite context: pointer-and-keyboard, dense tabular surfaces, the same interactions executed 50–500 times per day. The same motion that aids orientation on iOS becomes cognitive overhead in Close.io. Linear's refresh doctrine and Rauno's frequency argument converge on the same prescription for this context. See `_decisions-ledger.md § 2026-04-17 item 6` for the ledgered decision, and `foundations.md § Aesthetic-Usability Effect` for why "pretty" motion does not compensate for perceived latency at high frequency.

## Sources
- Rauno Freiberg, "Invisible Details of Interaction Design" (rauno.me/craft/interaction-design)
- Fluent 2 motion principles (fluent2.microsoft.design/motion)
- Linear design refresh 2026
