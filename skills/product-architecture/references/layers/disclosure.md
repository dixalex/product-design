# Disclosure

Fourth and final layer. Decides, per primary surface, *what is visible when* — always-on-screen vs. one-click-away vs. buried-in-settings. The layer where information density is dialed in.

## Core framing

Progressive disclosure operates at three scales:

1. **Interaction scale** (hover a cell, click to expand) — owned by `behavior-first-design`. Out of scope here.
2. **IA scale** (what goes on the surface vs. in a drawer vs. in settings) — **this skill's scope**.
3. **Data-model scale** (what metadata exists on the object but is never shown) — edge case; noted here but handled in Structure when it arises.

The skill's Disclosure gate works at scale #2 unless the user explicitly requests a data-model conversation.

## Canon

- **Brown's Principle of Disclosure (#3).** "Show only enough information to help people understand what kinds of information they'll find as they dig deeper." See `canon/brown-8-ia-principles.md`.
- **Polaris "Avoid information overload" (progressive disclosure at IA scale).** See `canon/polaris-ia-foundations.md`.
- **Tesler's Law (Conservation of Complexity).** Complexity cannot be eliminated, only moved. Disclosure is where you decide *where it lives*. Cross-ref `behavior-first-design/references/foundations.md § Tesler's Law`.
- **Nielsen heuristic #6 — Recognition over recall.** Make options visible; don't hide behind memory. External reference — no internal anchor in `behavior-first-design/references/foundations.md`. Source: https://www.nngroup.com/articles/ten-usability-heuristics/.
- **Nielsen — Progressive Disclosure (1995).** The originating source for the term. https://www.nngroup.com/articles/progressive-disclosure/.
- **Eagle §Progressive Disclosure (Pine internal).** "Default to compact, reveal on demand."

## Required output

Feeds the `## Disclosure` section of the handoff brief.

- `always_visible` — per primary surface, the content on screen at the moment of arrival.
- `one_click_away` — per primary surface, what's revealed on hover / click / peek.
- `two_plus_click` — per product, what's buried in settings, admin, or per-user preferences.

## Dialogue questions

Asked sequentially. Each question scopes to the *primary surface* identified at Structure Q3.

1. **"For your primary surface: what MUST be on screen when the user first sees it?"**
   - Expected shape: bullet list, ~5–12 items. Skill presses on any item the user can't justify ("what task does this support?").
2. **"What's valuable-but-not-essential — reveal on hover, click, or peek?"**
   - Expected shape: bullet list, typically 3–8 items. Skill maps each to an interaction primitive (hover / click / peek-drawer / dropdown) — but only as a *shape*; the actual component is `behavior-first-design`'s call.
3. **"What's admin / config / per-user preferences — willing to bury 2+ clicks deep?"**
   - Expected shape: bullet list. Shorter is better; items that don't earn their permanent existence should be deleted.
4. **"Anything the user will NEVER need to see in the product's lifecycle? Delete it."**
   - Expected shape: either a list of items being deleted, or an explicit "no" with justification. This is the Tesler's Law pressure question — forces the user to say out loud who absorbs complexity.

## Options pattern

Worked example — user's domain is CRM, domain peers are Linear + Close + Stripe.

- **Option A: Linear's dense-by-default pattern.** Everything of value on-screen at once — status, assignee, priority, labels, cycle all live in the list row. Progressive disclosure only for body / comments / activity (1 click → peek). Best when the primary persona is a power user who re-visits the same data all day and values information density over visual quiet. Tesler trade-off: user absorbs the parsing cost in exchange for never clicking to see the relevant fact.
- **Option B: Close's triage-visible pattern.** Inbox-style: today's actions always visible; historical leads one-click-away via peek; pipeline config 2+ clicks (settings). Best when the primary workflow is triage (high-volume inbound) and the "always visible" surface is a queue, not a directory. Tesler trade-off: system absorbs the prioritization cost (the queue sorts itself); user absorbs the cost of losing information when a lead scrolls off the queue.
- **Option C: Stripe's admin-buried pattern.** Minimal on first load (one core metric plus one CTA). Everything useful is behind progressive reveal. Best when the product has a broad user base with varying expertise and the default view should never overwhelm. Tesler trade-off: system absorbs the "smart default" cost; user absorbs more clicks for power-use.
- **Cross-domain surprise: Apple Mail-style "classic" vs. "focused" modes per-user.** The product ships two disclosure densities; users pick their own at onboarding. Higher engineering cost; higher user satisfaction when personas split cleanly.

For each option, the skill names the *Tesler trade-off* — which side absorbs the conserved complexity. This is the reason Tesler sits at the center of Disclosure canon even though the UX laws library usually files it under Interaction.

## Principles invoked

- **Brown #3 (Disclosure).** Q1–Q3 operate Brown's disclosure ladder at the IA scale.
- **Tesler's Law.** Q4 — the "willing to delete?" pressure question. Cross-link: `behavior-first-design/references/foundations.md § Tesler's Law`. Every disclosure decision has a loser; the skill names it.
- **Polaris "Avoid information overload."** Q1 + Q2 — the three Polaris moves (gradually reveal, multiple access points, eliminate redundancy) map 1:1 onto the three disclosure tiers.
- **Eagle §Progressive Disclosure.** "Default to compact, reveal on demand." The Pine internal echo of Brown #3. Applies as a tie-breaker when two tiers could work.
- **Nielsen heuristic #6 (Recognition over recall).** Q3 — items buried 2+ clicks must still be *recognizable* when encountered (clear labels, grouped). Hiding items behind ambiguous labels is a Nielsen #6 failure even if it passes Brown #3.

## Gate criteria

The skill does NOT proceed to handoff brief writing until:
- [ ] Per primary surface, an `always_visible` list exists with 5–12 items (Q1).
- [ ] Per primary surface, a `one_click_away` list exists (Q2). Empty lists are acceptable only with explicit "flat surface, no reveals" note.
- [ ] A `two_plus_click` list exists at the product level (Q3). Sparse is fine; missing is not.
- [ ] The Tesler question has been answered (Q4) — either items were deleted or the user has explicitly named which side of the trade-off they're accepting.
- [ ] User has confirmed the Disclosure block reads correctly.

## Transition prompt

The skill MUST emit this sentence verbatim once the gate criteria are met:

> **"Disclosure captured. Ready to write the handoff brief, or should I revise? (yes / revise)"**

If `revise`, the skill re-opens whichever sub-question. If `yes`, the skill assembles the handoff brief from all four layers per `handoff-brief-template.md` and writes it to `<project-root>/docs/product-architecture/<YYYY-MM-DD>-<project-slug>.md`.
