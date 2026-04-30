# Flows

Third layer. Defines how users *traverse* the surfaces named at Structure — entry points, global navigation, cross-surface jumps.

## Critical framing (state at top of every dialogue)

**Flows = navigation between surfaces, NOT within-task interaction.** Within-task interaction (inline-edit state machines, keyboard shortcuts within a list row, the timing of an autosave, hover-peek) is `behavior-first-design`'s turf. Flows cover wayfinding: how users reach a surface in the first place, how they know where they are, how they get back.

This clarifier is load-bearing. Without it, agents conflate "flow" (traversal) with "flow" (continuous task motion) and this skill silently duplicates `behavior-first-design`. See `_decisions-ledger.md` decision #3.

## Canon

- **Polaris "Show your audience where they are"** (wayfinding). Three nav schemes: structural, associative, utility. See `canon/polaris-ia-foundations.md`.
- **Brown's Principle of Front Doors (#5).** "Assume at least half of users will come through some page other than the home page." Every surface is a legitimate entry point. See `canon/brown-8-ia-principles.md`.
- **Brown's Principle of Focused Navigation (#7).** Don't mix kinds of things in a single nav scheme.
- **Peak-End Rule.** Users judge traversals by the peak and the ending — the entry and exit points carry disproportionate weight. Cross-ref `behavior-first-design/references/foundations.md § Peak-End Rule`.
- **Fitts's Law.** Primary nav targets should be big and close to where the user's cursor naturally sits. Cross-ref `behavior-first-design/references/foundations.md § Fitts's Law`.

## Required output

Feeds the `## Flows` section of the handoff spec.

- `primary_flows` — 2–4 user journeys, each written as `Surface → Surface → Surface → outcome`.
- `entry_points` — per named surface, the list of ways to reach it (URL, sidebar, cmd-K, search, notification, breadcrumb).
- `navigation_pattern` — the global chrome choice (sidebar / top bar / cmd-K / breadcrumbs), with a one-paragraph "why this over alternatives" note.

## Dialogue questions

Asked sequentially.

1. **"Walk me through the #1 task the user needs to finish. Name the surfaces they traverse."**
   - Expected shape: ordered list of surfaces with a one-line outcome at the end.
2. **"Repeat for 1–2 secondary tasks."**
   - Expected shape: 1–2 more ordered lists. Skill asks for second task unprompted; user can skip if only one primary flow matters.
3. **"Where can the user reach [primary surface] from? List entry points — URL, sidebar, cmd-K, search, etc."**
   - Expected shape: per primary surface, 3–6 entry points.
4. **"What global navigation do you want? Skill proposes 2–3 options grounded in domain peers."**
   - Expected shape: one chosen pattern, one-paragraph rationale.

## Options pattern

Worked example — user's domain is CRM, domain peers are Linear + Close + Attio.

- **Option A: Linear's global-nav pattern.** Left sidebar with top-level destinations (Inbox, My Issues, Projects, Views); cmd-K palette for everything else; no top bar. Scales cleanly up to ~10 sidebar items and then collapses into groups. Best when most user navigation is destination-stable (the user knows which page they want) and cmd-K handles the long tail. Fitts's Law friendly: sidebar targets are large and close; cmd-K is one chord away from anywhere.
- **Option B: Close's global-nav pattern.** Left sidebar with inbox-style triage at the top (Unread, Today, Queue), pipeline views below, settings buried in the bottom region. Best when the product has a high-volume daily workflow (email triage, lead queue) and the sidebar *is* the workflow — the user spends their day inside sidebar-rooted surfaces. Peak-End friendly: the "ending" of every task is a return to the queue, which stays present in peripheral vision.
- **Option C: Attio's global-nav pattern.** Top bar with workspace switcher + cmd-K; no persistent sidebar; each surface owns its own local nav. Best when workspaces differ schematically and a single sidebar can't serve them all. Higher learning cost; higher schema flexibility.
- **Cross-domain surprise: Slack's channels-as-navigation.** Lists replace sidebar items; each channel/list carries its own state and read-markers. Radical for CRM, potentially high-upside if the user has many named segments (e.g. "Warm EU enterprise," "Cold inbound US") they switch between like channels. Rarely the default; offer only on request.

For each option, the skill names the *cost* as plainly as the benefit: Linear's pattern hides the long tail behind cmd-K (bad for discoverability); Close's pattern conflates object nav and task queue (bad if the queue gets long); Attio's pattern drops persistent orientation (bad for cold-arrival users).

## Principles invoked

- **Polaris wayfinding.** Q1 + Q3 — name the traversal; name the doors. See `canon/polaris-ia-foundations.md § Show your audience where they are`.
- **Brown #5 (Front Doors).** Q3 — the skill enforces that every primary surface has at least 2 entry points (one canonical, one alternative). Single-entry surfaces fail the Front Doors check.
- **Brown #7 (Focused Navigation).** Q4 — the skill validates that proposed nav items don't mix objects with actions with utility at the same level. The Close "inbox + pipeline + settings" sidebar works only because each region is internally focused (queue items on top, objects in middle, utility on bottom).
- **Peak-End Rule.** Q1 + Q2 — skill probes for the "end" of each flow: when the user completes the task, where are they returned to? A flow that ends at a blank page fails Peak-End. Cross-link: `behavior-first-design/references/foundations.md § Peak-End Rule`.
- **Fitts's Law.** Q4 — when the skill proposes options, each option's Fitts trade-offs are named. Cross-link: `behavior-first-design/references/foundations.md § Fitts's Law`.

## Gate criteria

The skill does NOT proceed to Disclosure until:
- [ ] 2–4 primary flows written, each with a named end-state (Q1 + Q2).
- [ ] Every surface named at Structure has ≥2 entry points listed (Q3). If the user insists on a single-entry surface, the skill notes it as an exception and asks the user to confirm they understand the Front Doors trade-off.
- [ ] Global navigation pattern chosen, with one-paragraph rationale (Q4).
- [ ] The chosen navigation pattern has been tested against Brown #7 — no mixed-kind items at the same level.
- [ ] User has confirmed the Flows block reads correctly.

## Transition prompt

The skill MUST emit this sentence verbatim once the gate criteria are met:

> **"Flows captured. Ready to move to Disclosure, or should I revise? (yes / revise)"**

If `revise`, the skill re-opens whichever sub-question. If `yes`, the skill moves to Disclosure — see `layers/disclosure.md`.

## Mermaid output (optional but recommended)

After writing the Flows prose, emit a Mermaid block. Pick the diagram type to match the flow shape:

- `flowchart TD` for branching navigation
- `sequenceDiagram` for time-ordered interactions
- `stateDiagram-v2` for surface state machines (open / focused / dismissed)

Renders everywhere modern Markdown does; degrades to text in CLI.
