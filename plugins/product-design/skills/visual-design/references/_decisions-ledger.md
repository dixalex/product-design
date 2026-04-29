# Decisions ledger — visual-design

Resolved design decisions for the skill itself (not for any given project).
Consult BEFORE re-deciding. Append new decisions in reverse-chronological order.

## 2026-04-26

1. **Skill is a process skill, not a reference library.** Mimics `superpowers:brainstorming` and sibling `product-architecture`. Gates between layers; terminates in a handoff spec. Source: spec `2026-04-26-visual-design-skill.md § 3.1`.
2. **6 layers: Mood → Typography → Color → Spacing → Motion → Polish.** Mood is Q0-equivalent (load-bearing upstream commitment). Polish splits into (a) depth system + (b) signature details. Source: spec § 4.
3. **Pipeline position: between product-architecture and behavior-first-design.** Inversion of Garrett's classical Strategy → Surface order. Source: spec § 2. Exception path for performance/input-constrained surfaces in spec § 2.5.
4. **Derivation method per layer.** Mood: extract from JTBD's emotional verbs + persona's tool-adjacency + scope-as-feature exclusions. Typography: voice-personality mapping. Color: semantic-contrast derivation. Spacing: density-rhythm derivation. Motion: behavior-frequency × mood-energy matrix. Polish: distinctive-residual derivation. Source: spec § 4.
5. **Brief format: prose decisions + CSS tokens block.** Tokens are upstream truth; user passes them to frontend-design as explicit context. No wrapper magic. Source: spec § 6.
6. **User-supplied voices land project-local** at `<project-root>/docs/visual-design/voices/<slug>.md`. Skill canon stays slow-moving; auto-promotion to canon explicitly forbidden. Source: spec § 7.
7. **4 ship-with canon files** (Refactoring UI, Rams, Lupton, Apple HIG Foundations) + **4 ship-with voice files** (Linear, Stripe, Operate, Rauno-micro-details). Source: spec § 7.
8. **Tokens migrated from behavior-first-design at v0.3.0** including motion duration literals (150ms / 180ms / 200ms / 0ms). Source: spec § 8.
9. **Mid-session checkpoint after Color (layer 3)** — opt-in pause to avoid 6-layer fatigue. Source: spec § 5 step 9.
10. **Frontend-design contract is honest, not enforced.** Frontend-design freelances by design; tokens block is upstream truth, but the user passes it as context — no wrapper. Source: spec § 6.

<!-- Append new decisions above. -->
