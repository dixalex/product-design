# Decisions ledger — product-architecture

Resolved design decisions for the skill itself (not for any given project).
Consult BEFORE re-deciding. Append new decisions in reverse-chronological order.

## 2026-04-21

1. **Skill is a process skill, not a reference library.** Mimics `superpowers:brainstorming`. Gates between layers; terminates in a handoff spec. Source: spec `2026-04-21-product-design-plugin.md § 3.1` (pine-research).
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

## 2026-04-29 (v0.4.0)

1. **Added Glossary (Ubiquitous Language) section** to structural-spec template. Locks domain vocabulary at Layer 2; downstream specs consume verbatim. Source: spec `2026-04-28-product-design-plugin-v0.4.0-design.md § 2.6`. Pocock/Evans (DDD) framing.

## 2026-04-30 (v0.4.1)

1. **Added Mermaid output guidance** to Structure + Flows layer files; templates updated. Optional but recommended; renders in GitHub / claude.ai / Cowork; degrades to text in CLI. Source: spec `2026-04-30-product-design-plugin-v0.4.1-design.md § 1.1`.
2. **Added Ousterhout canon doc** at `canon/ousterhout-deep-modules.md`. Cross-referenced from Structure layer's Round-3 collapse step. Pocock + Beck framing in v0.4.0 SKILL.md prose now backed by proper canon file. Source: spec § 1.4.
