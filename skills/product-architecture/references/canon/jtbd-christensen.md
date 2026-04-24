# JTBD — Jobs-to-be-Done (Christensen)

- **Author/source:** Clayton M. Christensen, Taddy Hall, Karen Dillon, and David S. Duncan, *Competing Against Luck: The Story of Innovation and Customer Choice* (2016, HarperBusiness). Christensen's earlier formulation via the "milkshake" essay and *The Innovator's Solution* (2003). Further formalization by Tony Ulwick / Strategyn via Outcome-Driven Innovation (ODI): https://strategyn.com/jobs-to-be-done/.
- **Statement:** People don't buy products; they "hire" them to make progress in a specific situation. The canonical JTBD statement is: *"When [situation], I want [motivation], so I can [expected outcome]."*
- **Applies to layer:** Intent.
- **Why it matters:** JTBD is the dominant 2024–2026 input format for the Intent layer. It produces a statement that is (a) user-centered, (b) agent-readable, (c) portable across downstream layers. More importantly, it extracts what the user is *actually* trying to do — progress toward an outcome in context — rather than what they claim they want as a feature. "Make the fire louder" (feature) becomes "when I wake to a cold house, I want to warm the room quickly, so I can get out of bed" (job).
- **How the skill uses it:** The Intent gate produces exactly one JTBD statement per primary persona. That statement then seeds every downstream layer: OOUX noun-extraction runs against it at Structure; primary flow narration uses it at Flows; always-visible content at Disclosure is tested against whether it supports the hiring moment.

## Full principle(s)

### The Job Statement format

Christensen's canonical form (as formalized by Ulwick and others):

```
When [situation],
I want [motivation],
so I can [expected outcome].
```

The expected outcome is both functional (what gets done) and emotional (how it feels when done). Example for a CRM:

> *When I start my day and I have 40 unread replies across 12 deals, I want to triage them by urgency and move the 5 most valuable forward in minutes, so I can feel in control of my pipeline and stop dropping the ball on the best leads.*

The statement names:
- A situation (trigger, context).
- A motivation (the force pulling the user forward).
- An outcome (functional progress + emotional payoff).

What it deliberately *doesn't* name: features, screens, UI elements. JTBD stays above the feature layer — the rest of the skill extracts features from the job, not the other way around.

### IDEO's 3-lens companion

A JTBD statement, on its own, is a desirability claim ("users want this"). Christensen's framework pairs naturally with IDEO's Three Lenses — *desirability × feasibility × viability* — for a complete Intent read:

- **Desirable:** Do users have this job? Would they hire this product to do it? (JTBD handles this.)
- **Feasible:** Can we build it? Is the tech available; is the team capable?
- **Viable:** Does it survive as a business? Revenue model, sustainability.

The Intent gate captures a short paragraph per lens; the skill rejects an Intent that is desirable but neither feasible nor viable.

### Why JTBD at Intent, not elsewhere

JTBD is not a layer; it's a *tool* for populating the top layer. It does not describe object models, navigation, or disclosure. It answers exactly one question: "what is this product hired to do?" That question is the Intent question. Once answered, the rest of the product-architecture skill is downstream.

### Caveats: Christensen's contested influence

Christensen's broader innovation theory (disruption theory, especially) has been heavily contested in academic and industry writing since 2014 — Jill Lepore's *New Yorker* critique and subsequent empirical re-analyses have weakened the evidentiary base for "disruptive innovation" as a predictive framework. JTBD as a product-design tool is a narrower and more durable idea: the framing question "what is this product hired to do?" is useful regardless of whether Christensen's broader theses hold up. The skill treats JTBD as *one of several* Intent frameworks — alongside Linear's "Direction," Basecamp's "Shape Up" pitch, and IDEO's 3 lenses — rather than the only one. If the user prefers to write Intent as a Linear-style Direction statement or a Shape Up pitch, the skill accepts that and proceeds.

### The Intent → Structure bridge (OOUX cross-link)

The load-bearing move, downstream of JTBD: once the job statement exists, the skill runs a noun-extraction pass against it (per OOUX). The nouns in the job statement become the candidate objects at the Structure gate. This is how JTBD "cashes out" into a working object model — without OOUX, JTBD is philosophically nice but produces no concrete next move. See `canon/ooux-objects-before-actions.md`.

## Citation

Christensen, C. M., Hall, T., Dillon, K., & Duncan, D. S. (2016). *Competing Against Luck: The Story of Innovation and Customer Choice.* HarperBusiness. ISBN 978-0-062-43561-1.

Earlier foundational work:
- Christensen, C. M., & Raynor, M. E. (2003). *The Innovator's Solution: Creating and Sustaining Successful Growth.* Harvard Business Review Press.
- Ulwick, A. W. (2005). *What Customers Want: Using Outcome-Driven Innovation to Create Breakthrough Products and Services.* McGraw-Hill. Ongoing at: https://strategyn.com/jobs-to-be-done/.

Companion framings:
- IDEO. "Design Thinking" resource: https://designthinking.ideo.com/ (Three Lenses: Desirability × Feasibility × Viability).
- Saarinen, K. "10 Rules for Crafting Products That Stand Out." Figma Blog: https://www.figma.com/blog/karri-saarinens-10-rules-for-crafting-products-that-stand-out/ ("Design for someone, not everyone.")
