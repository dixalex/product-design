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

## Resolved

- **v1.1 skill-hygiene follow-ups** — APPLIED 2026-04-25. SKILL.md trimmed from 1,989 to 1,482 words and frontmatter description rewritten to WHAT+WHEN (522 chars, 8 phrases, explicit exclusion) per Anthropic guideline. See `pine-research/research/plugin-best-practices/SYNTHESIS.md` and commit history.
