# Foundations — 10 cited laws

Every behavior principle in SKILL.md traces to one of these laws. Each entry follows the law-template from `_templates.md § Law template`.

### Nielsen's Response Time Triad

- **Source:** Jakob Nielsen, 1993, *Usability Engineering* Ch. 5 ("Response Times: The 3 Important Limits"), building on Robert B. Miller, 1968, "Response Time in Man-Computer Conversational Transactions," AFIPS Fall Joint Computer Conference.
- **Statement:** Three human-perception thresholds govern interactive feedback — **0.1s** feels instantaneous (direct manipulation); **1.0s** keeps the user's flow of thought uninterrupted; **10s** is the outer bound for holding attention on a task.
- **CRM implication:** Every interaction in a Linear-style product must land under 100ms on the happy path, or it stops feeling like direct manipulation and starts feeling like a request-response transaction. Above 1s you must show a working cursor; above 10s a progress indicator with a cancel path. Nielsen's 2014 addendum reaffirmed these limits have held for 46 years — they are not likely to shift with whatever framework comes next, so budget against them.
- **Used by principle:** Principle 1 (feels instant).

### Doherty Threshold

- **Source:** Walter J. Doherty & Ahrvind J. Thadani, 1982, "The Economic Value of Rapid Response Time," *IBM Systems Journal*.
- **Statement:** Productivity and user engagement rise sharply when machine response time drops **below 400ms**, so neither human nor computer has to wait on the other.
- **CRM implication:** 400ms is the practical budget for any action that cannot hit Nielsen's 100ms — server roundtrips, cross-region fetches, heavy rendering. Anything that cannot beat 400ms needs **optimistic UI** (render the success state immediately, reconcile on server reply) or **perceived-performance** tricks (skeletons, progressive reveal, purposeful animation). The IBM paper famously dropped the industry standard from 2000ms to 400ms by showing sub-400ms responses were *addicting*; the same finding tells us why Linear and Superhuman feel qualitatively different from tools that sit in the 1-2s band.
- **Used by principle:** Principle 1 (feels instant).

### Fitts's Law

- **Source:** Paul M. Fitts, 1954, "The information capacity of the human motor system in controlling the amplitude of movement," *Journal of Experimental Psychology* 47(6):381–391.
- **Statement:** The time to acquire a target is a logarithmic function of the distance to the target and inversely related to its size — smaller and farther targets cost more time and produce more errors.
- **CRM implication:** Oversize primary actions (they are cheap visually and expensive to miss); group related targets so the pointer path between them is short; edge-pin and corner-pin high-frequency targets because screen edges behave as infinitely tall targets (the Mac menu bar is the canonical example). Keyboard shortcuts sidestep the law entirely, which is why power-user CRMs lean on them for the top-20 actions.
- **Used by principle:** Principles 2 (obvious affordances) and 6 (keyboard-first / shortcut-driven).

### Hick's Law

- **Source:** William E. Hick, 1952, "On the rate of gain of information," *Quarterly Journal of Experimental Psychology*; Ray Hyman, 1953, "Stimulus information as a determinant of reaction time," *Journal of Experimental Psychology*.
- **Statement:** The time required to make a decision grows logarithmically with the number and complexity of available choices.
- **CRM implication:** One primary action per view. Collapse secondary actions behind menus and progressive disclosure. Reduce visible options on critical paths — the signup, first-run, and convert-a-lead paths should each present a single obvious next step. Slack's onboarding and the Apple TV remote both pay the complexity cost inside the interface so the user pays less; that is the move. Split long flows into steps so each decision stands alone with fewer competitors.
- **Used by principle:** Principle 6 (progressive disclosure, one primary action).

### Miller's Law

- **Source:** Nelson Cowan, 2001, *"The Magical Number 4 in Short-Term Memory: A Reconsideration of Mental Storage Capacity,"* Behavioral and Brain Sciences 24(1):87–114 — revising George A. Miller, 1956, *"The Magical Number Seven, Plus or Minus Two: Some Limits on Our Capacity for Processing Information,"* Psychological Review 63(2):81–97.
- **Statement:** Raw working-memory capacity is **~4 chunks** without strategic aid (Cowan 2001); Miller's 7±2 (1956) overstated capacity because the tasks allowed chunking, rehearsal, and long-term-memory recoding, which Cowan's articulatory-suppression experiments stripped out to isolate a narrower 3–5-chunk core centered on 4.
- **CRM implication:** **Chunk forms, menus, and navigation into 4 items per group, not 7.** Command palette top-level commands: keep within 4 visible without scroll. List-row inline actions: 4 or fewer before overflow. Form fields: group into sections of ~4, not long undifferentiated columns. Filter chips, tabs, primary nav items — all calibrate against 4, with 7 as a hard ceiling only when the items are individually salient (e.g., one-word labels). The Miller 7±2 heuristic is widely cited in UX writing but has been formally superseded; we cite Cowan for accuracy and Miller for lineage. See `_decisions-ledger.md § 2026-04-17 item 11`.
- **Used by principle:** Command-palette command count; list-row inline action count; form-field grouping.

### Jakob's Law

- **Source:** Jakob Nielsen, Nielsen Norman Group, "Jakob's Law of Internet User Experience."
- **Statement:** Users spend most of their time on *other* products, so they expect yours to work the same way as the ones they already know.
- **CRM implication:** Convention over novelty on every load-bearing interaction. Cmd-K opens the command palette. `/` focuses search. Shift-click extends a selection. Email threads reverse-chron. Pipelines run left-to-right. Where a user's mental model was set by Gmail, Linear, Notion, and Superhuman, adopt that mental model — deviations must pay for themselves in measurable gain, not aesthetic preference. When you must break convention (YouTube's 2017 Material redesign is the reference case), give a preview-and-revert off-ramp so users can reconcile on their own schedule.
- **Used by principle:** Principle 3 (conventional patterns) and the stack-bindings/web-primitives keyboard contracts.

### Norman's Affordances + Signifiers

- **Source:** Donald A. Norman, 1988/2013, *The Design of Everyday Things*; affordance concept originally from James J. Gibson, 1977, "The Theory of Affordances."
- **Statement:** **Affordances** are the action possibilities an object offers an actor; **signifiers** are the perceivable cues that communicate *where* and *how* the action can be performed. Both are required — affordances without signifiers hide; signifiers without affordances mislead.
- **CRM implication:** Every interactive element must announce itself. Buttons look like buttons (shape, weight, hover). Links carry underlines or color deltas strong enough to read as non-body-text. The cursor must change on hover to every clickable target. Disabled states must *look* disabled, not merely be disabled. Icon-only buttons need accessible names (tooltip or aria-label). A "push" label on a "pull" door is the exact same pathology as a non-interactive chip styled like a button, or a button styled like plain text — fix both.
- **Used by principle:** Principle 2 (obvious affordances).

### Tesler's Law

- **Source:** Larry Tesler, Xerox PARC, mid-1980s; canonicalized in Dan Saffer, 2009, *Designing for Interaction*.
- **Statement:** Every system has an irreducible amount of complexity; the only question is who absorbs it — the product, or the user.
- **CRM implication:** Absorb complexity inside the product so users do not have to carry it. Smart defaults over configuration. Auto-detect over ask. Infer timezone, locale, currency, and pipeline stage from context before prompting. "An engineer should spend an extra week reducing complexity rather than making millions of users spend an extra minute." In a CRM, this means the new-lead form pre-fills company from email domain, the deal-stage form proposes the next stage, and the activity logger picks the right record by default — because the alternative is every user making that same inference on every record forever.
- **Used by principle:** Principle 7 (smart defaults / zero-config).

### Aesthetic-Usability Effect

- **Source:** Masaaki Kurosu & Kaori Kashimura, 1995, "Apparent Usability vs. Inherent Usability: Experimental analysis on the determinants of the apparent usability," CHI '95 Conference Companion. Hitachi Design Center ATM study: 252 participants, 26 interface variations.
- **Statement:** Users rate more aesthetic interfaces as easier to use — even when objective usability is held constant.
- **CRM implication:** Visual polish earns trust and forgiveness. Spacing, type, color restraint, motion quality, and empty-state craft all compound into *perceived* usability, which shapes retention and tolerance for defects. Two caveats: (1) aesthetics mask usability problems during unstructured testing, so test functionally (task completion, time, error rate) with data-model-instrumented tasks, not vibes-based walkthroughs; (2) polish is table stakes at the level of Linear and Superhuman — the bar is not "looks professional" but "feels considered at every pixel."
- **Used by principle:** Principle 4 (polish / craft as behavior) and motion.md.

### Peak-End Rule

- **Source:** Daniel Kahneman, Barbara L. Fredrickson, Charles A. Schreiber & Donald A. Redelmeier, 1993, "When More Pain Is Preferred to Less: Adding a Better End," *Psychological Science* 4(6):401–405.
- **Statement:** People judge an experience largely by how they felt at its **peak** (most intense moment) and at its **end**, not by the average of the whole.
- **CRM implication:** Over-invest in confirmation states, success moments, error-recovery, and last-step polish — these are the moments users will encode and recall. The Mailchimp post-send high-five and Uber's post-request confirmation screen are load-bearing *because* they are at the end, not despite it. Inversely, a clumsy final step (a truncated toast, a silent failure, an ugly empty state after a successful bulk action) will define the memory of the entire flow. Design the finish line and the peak with disproportionate care; the middle can be merely competent.
- **Used by principle:** Principle 5 (craft the peak and end — confirmation, error recovery, empty states).
