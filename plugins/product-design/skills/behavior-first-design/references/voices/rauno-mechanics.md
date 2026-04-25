# Voice: Rauno Freiberg — Invisible Details of Interaction Design

## Source

- Rauno Freiberg, "Invisible Details of Interaction Design" (July 2023) — https://rauno.me/craft/interaction-design
- Local scrape: `pine-research/.firecrawl/scratch/rauno-interaction-design.md`

## Core quotes (verbatim, attributed)

> "If we for a moment consider the interaction frequency being hundreds of times a day, it does start to feel more like cognitive burden after seeing the same animation for the hundredth time."
> — Rauno Freiberg, 2023 (on command menus)

> "When so commonly executed, the interaction novelty is also diminished. It doesn't feel like you're doing anything peculiar, deserving of a special flourish."
> — Rauno Freiberg, 2023

> "Lightweight actions, such as displaying overlays, feel more natural to trigger during the swipe after an arbitrary amount of distance… Waiting for the gesture to end would feel inappropriate here."
> — Rauno Freiberg, 2023 (on gesture triggers)

> "Now, let's look at examples where triggering an action requires explicit intent. The iOS App Switcher will never dismiss an app before the gesture ends. No matter the distance or the fact that the app is partially off-screen… dismissing an app is destructive."
> — Rauno Freiberg, 2023

> "Truly fluid gestures are immediately responsive… gestures can have an explicit trigger threshold, but this does not mean simply performing an animation 0 → 1 would feel great."
> — Rauno Freiberg, 2023

> "Imagine if it were an animation that you had to wait for!"
> — Rauno Freiberg, 2023 (on interruptibility, re: flipping a page in a book)

## Actionable principles

1. **Frequency beats novelty — remove motion from high-frequency paths.** An animation that delights on first use becomes a tax on the hundredth. Command palette open, row select, arrow-key nav, apply-filter, save — these fire hundreds of times a day. Default to *no animation* on these paths. Reserve motion for rare, novel, or spatially-discontinuous moments (a Peek sliding in; a Lead being first created). This overrides any "always animate for consistency" instinct.

2. **Interruptibility is a hard rule.** Any gesture or transition the user can start must be interruptible *during* the animation — reversing mid-swipe cancels, tapping during a fade commits the final state immediately. If the user has to "wait for the animation to finish", the interaction is broken. Apply to Peek, dropdown, dialog, drawer — all of them.

3. **Choose gesture triggers by action cost.** Lightweight, reversible reveals (filter overlay, tooltip, Peek) trigger *during* the gesture after a threshold — the user learns the surface and can dismiss immediately. Destructive or committing actions (delete, close tab, dismiss unsaved draft) trigger *only on release*, with a distance threshold requiring explicit intent. iOS App Switcher is canonical: it never dismisses mid-swipe because losing work by accident is unforgivable.

4. **Gestures must feel responsive before crossing the threshold.** An element that sits frozen then snaps on threshold gives no affordance it is manipulable. Apply the transform (scale, translate, rotation) continuously as input changes; trigger the discrete state change on threshold. Zero-to-one step functions feel broken even when the threshold is correct.

5. **Animate to clarify spatial relationships; don't animate to decorate.** A Peek sliding in from the right earns its motion — it tells the user *where* the Peek is spatially. An opacity fade on a context menu earns nothing; it delays access to items the user has already chosen. If the motion does not encode a spatial or semantic fact, cut it.

6. **Micro-details pay rent only if perceived.** Details no user consciously notices but that affect *feel* are worth shipping. Details no one notices that don't change feel are cost without revenue.

7. **Match motion to meaning.** iOS's app-to-Dynamic-Island dismissal retains thrown momentum; a playing card bounces less because it is conceptually lighter. In a CRM: a Lead settling into "Archived" decays with weight matching the element — short snap for a chip, longer settle for a full row.

## When to load this voice

Load `voices/rauno-mechanics.md` when any of these apply:

- **Any motion decision**, especially "should this be animated at all?" (Pair with `motion.md` for the quantitative companion.)
- **List / row interaction** — arrow-key nav, hover reveal, selection, inline edit.
- **Peek / drawer / side-panel** — slide-in direction, interruptibility, dismiss.
- **Gesture design** — swipe thresholds, drag affordances, release-to-commit vs. during-gesture.
- Anywhere **frequency > novelty** — the user does this dozens or hundreds of times per day.
- User says **"feels sluggish"** or **"why does this need an animation?"** — the diagnostic voice.
- **Command menu / command palette** — Rauno's specific worked example.
- Decisions about **interruptibility** mid-transition.

See also: `motion.md § prefers-reduced-motion`, `_decisions-ledger.md § item 6` (Rauno > HIG on frequency-based restraint), `voices/superhuman.md § Actionable principles (principle 6)`.
