# What's next for behavior-first-design

## v1.1 deferred components
- DataGrid
- Drawer (Base UI has primitive — low cost add)
- Feed / Activity stream
- Editor (TipTap-based)
- Peek (composite — partial in mini-lead-crm; could generalize)
- ContextMenu (covered by DropdownMenu in v0; may warrant dedicated file for right-click semantics)
- Tabs

## Product-architecture sibling skill scope
Eagle's 17 architectural sections. Plus:
- Close.io data model observations (`pine-research/research/close-io-raw/`)
- Operate object shape (`pine-research/research/operate-design/`)
- Sync + offline patterns (local-first)
- Multi-view architecture (list / kanban / calendar)
- Extension / plugin lifecycle

## Pine IRM readiness
Behavior-first-design must additionally cover before real IRM redesign starts:
- Influencer-specific patterns (multi-platform identity, payment tracking)
- Feed/Activity (deferred to v1.1)
- Real-time collaborative cursors (cite Yjs / Automerge patterns)

## Risk follow-ups
- Content drift — schedule quarterly spot-check against Linear/Operate updates
- Claude model variance — rerun trigger-phrase smoke test per new model

## v1.1 skill-hygiene follow-ups (from 2026-04-21 plugin best-practices review)

Research: `pine-research/research/plugin-best-practices/SYNTHESIS.md` (if/when repo-relative link breaks after plugin extraction, see the commit history).

- **Trim SKILL.md.** Currently 1,989 words. Anthropic guideline is <500 lines / ≤1,500 words. Above the threshold, Claude starts following the description and skipping the body (documented failure mode). Candidate trims: move per-principle expansion prose into `references/principles/`; shorten the "What this skill is, cognitively" section from 3 paragraphs to 1; collapse the Reference index table into a terse pointer.
- **Trim frontmatter description.** Currently ~400 chars with 10 trigger phrases. Canon is ~500-600 chars WHAT+WHEN shape with ≤8 phrases. We're under the char limit but the structure is "summarize the workflow" — rewrite to WHAT+WHEN + explicit exclusion (e.g. "NOT for full-product design; use product-architecture").
- Defer: not urgent — no evidence it's hurting in practice. Ship in v1.1 alongside other behavior-first-design revisions.
