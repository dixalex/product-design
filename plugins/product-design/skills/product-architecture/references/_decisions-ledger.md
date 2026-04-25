# Decisions ledger — product-architecture

Resolved design decisions for the skill itself (not for any given project).
Consult BEFORE re-deciding. Append new decisions in reverse-chronological order.

## 2026-04-21

1. **Skill is a process skill, not a reference library.** Mimics `superpowers:brainstorming`. Gates between layers; terminates in a handoff brief. Source: spec `2026-04-21-product-design-plugin.md § 3.1` (pine-research).
2. **Layer model: Intent → Structure → Flows → Disclosure.** Validated against canon in `research/product-architecture/SYNTHESIS.md` (pine-research). Garrett, Brown, OOUX, Polaris converge on this shape.
3. **Flows = navigation between surfaces, NOT within-task interaction.** Within-task is `behavior-first-design`'s turf. This clarifier is load-bearing — without it, agents conflate the two skills.
4. **Dan Brown's 8 IA Principles (2010) = primary canon for Structure.** Polaris is the only peer design system that explicitly names IA as a foundation — this skill fills a real gap.
5. **OOUX "Objects before actions" = strongest Structure-layer principle.** Noun-extraction from JTBD bridges Intent→Structure.
6. **Cross-reference, don't duplicate.** 6 of the 10 UX laws in `behavior-first-design/foundations.md` (Hick, Miller, Jakob, Tesler, Fitts, Peak-End) fire at product-architecture layers. Cross-link, don't copy.
7. **Reference library `patterns-by-domain/` deferred to dedicated session.** v1 asks user for domain peers inline; library accretes later.
8. **Feature prioritization OUT of scope.** That's roadmap/execution — separate problem.
9. **Handoff brief shape: Markdown + YAML front-matter.** Front-matter = machine-readable (domain, JTBD, surfaces). Prose = human. Handoff table = routing layer for behavior-first-design.
10. **Domain awareness at Intent gate.** Domain = required output; gates everything downstream. v1 asks user; v2 may infer.

<!-- Append new decisions above. -->
