# Rauno-micro-details — Rauno Freiberg, designer at Vercel

- **Source:** rauno.me, rauno.me/craft, rauno.me/craft/interaction-design
- **Captured:** 2026-04-26
- **Layer relevance:** Polish (PRIMARY canon for micro-details), Motion (when motion fires + how it communicates state)

## Aesthetic profile

Rauno's thesis is "details that pay rent." Every micro-detail — a focus ring, a hover transition, a scroll-snap curve, a custom cursor moment — must either communicate state or improve perceived responsiveness. If it does neither, it gets cut. This is the opposite of polish-for-polish's-sake: it's a discipline where adding a detail requires justification, and the justification is always functional. The result looks lavish because every choice is deliberate, not because there are many choices.

Motion communicates state-change or compresses perceived latency — nothing else. A popover slides in because the direction tells the user where the layer sits in the stack. A button press releases with a spring because the rebound communicates "action registered." A skeleton loads because it fills time while data arrives, anchoring layout so the shift doesn't jar. Motion that exists to "feel modern" or "feel alive" is the anti-thesis: it consumes attention without returning information, making the UI louder and the user slower.

The signature move is layered details that compound. A focus ring: 1px outer outline + 2px middle ring + accent fill in the gap. A button press: color shift + scale reduction + shadow collapse — three signals firing simultaneously. A scroll hint: animated only at the moment the user is about to miss a cue, then absent everywhere else. Individually, each layer is a small decision. Compounded, the experience reads as "how is this so much better than what I usually use?" — a question users ask without knowing why. That unarticulated quality gap is what layered craft produces.

## Per-layer treatment

### Polish

- Decision: layered focus rings (1px outer + 2px middle + accent fill), multi-state transitions that stack signals (color + scale + shadow on press), custom cursors introduced only at semantically meaningful moments, scroll-snap with momentum-friendly easing rather than abrupt stops.
- Why it works: each individual layer is small — no single addition overwhelms. The cumulative compound effect crosses a threshold that single-detail approaches never reach. Users perceive quality without identifying the source, which means the craft reads as product quality rather than designer flourish.
- When NOT to copy: products without staffing to hand-tune and maintain layered details across component states and breakpoints. Performance-constrained surfaces — mobile, low-end devices, slow connections — where layered transitions accumulate frame-budget debt. High-volume component libraries where each layered detail multiplies maintenance cost across dozens of contributors. Teams where the perceived-quality return from layered polish is lower than the maintenance overhead it demands.

### Motion

- Decision: motion exists to communicate state, not to entertain. Entry elements ease out (fast start, gradual settle); exiting elements ease in (gradual release, fast exit). Layered durations by register: UI chrome eases at 150ms; content elements ease at 250ms; spring easing reserved for celebratory moments only (confetti, success states). `prefers-reduced-motion` compliance is non-negotiable — not a flag, not an afterthought, wired into every animated component at authoring time.
- Why it works: directional easing matches physical expectation (things enter with momentum, leave with effort). Duration tiering prevents UI chrome from dragging at content pace or content from snapping at chrome pace. The motion vocabulary is small, so every departure from it reads as intentional signal.
- When NOT to copy: consumer products where decorative motion is the value proposition (short-form video, immersive media, game UI). Products targeting audiences where `prefers-reduced-motion` adoption is high and motion would degrade the primary experience. Anywhere the team cannot author motion with reduced-motion compliance baked in from the start — retrofitting is error-prone and often incomplete.

## Citation

- rauno.me (homepage + portfolio)
- rauno.me/craft (craft essays)
- rauno.me/craft/interaction-design ("Invisible details of interaction design")
