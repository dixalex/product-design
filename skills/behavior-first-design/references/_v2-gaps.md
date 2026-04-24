# Intentional gaps — planned for v2+

## Mobile / responsive behavior specs
Not in v0. Desktop-first matches Linear/Operate scope. v2 will cover:
- Touch targets (44px minimum, Fitts' Law on touch)
- Gesture patterns (swipe to archive, pull to refresh)
- Responsive breakpoints + behavior adjustments
- Virtual keyboard handling for inline editing

Location reserved: `references/mobile/` (create when needed).

## Audit/review workflow (Pattern B)
Not in v0. Reference-on-build only. See `_decisions-ledger.md § 2026-04-17 item 10` for rationale. v2 introduces `audit.md` + agent that reads code against the 8 principles.

## Component coverage beyond Common 15
Promoted to v0 on 2026-04-21 (see `_decisions-ledger.md § 2026-04-21`):
- ~~Drawer~~ → shipped (`components/drawer.md`)
- ~~Peek~~ → shipped (`components/peek.md`, surfaced by `examples/mini-lead-crm/components/lead-detail-peek.tsx`)
- ~~Tabs~~ → shipped (`components/tabs.md`)
- ~~ContextMenu~~ → functionality covered by DropdownMenu in v0; no separate component planned

Still deferred to v1.1:
- DataGrid
- Feed / Activity stream
- Editor (TipTap-based)

## Product architecture skill
Separate upcoming skill at `pine-research/skills/product-architecture/`. Will cover:
- Data model patterns
- Ingestion pipelines
- Multi-view architecture
- Sync + offline
- Plugin/extension lifecycle
- (Eagle's 17 architectural sections, not covered in behavior-first)

## Second token set
`tokens/neutral-linear-style.md` — Linear-inspired alternative for teams without Operate's Muoto license.
Deferred to v1.1.

## Post-scaffold deep research
Multi-subagent research on the full product-development pipeline (sequencing of skills, pipeline gaps, what exists vs what's needed). Parked in task #33 of the session task list. Runs after v0 ships.

## v0 scope-correctness signal (Musk 10% rule)

Per first-principles "delete before simplify" methodology: if fewer than **10% of deleted v0 items get re-added in v1.1, we deleted too few** (over-conservative scope). If more than 10% get re-added, we deleted too many (over-aggressive cut).

- Deleted from v0 Common 15: context-menu, tabs, composites (moved to patterns/), neutral-linear-style tokens. Deleted count ~4.
- 10% of 15 components = 1.5 items. Target re-add range at v1.1: **0–2 components**.
- If we re-add 3+ components in v1.1 from this deferred list (DataGrid, Drawer, Editor, Feed, Peek, ContextMenu, Tabs), the v0 Common-15 was probably miscalibrated — note the pattern for future scope decisions.

**Measurement result (2026-04-21):** Promoted 3 components (Peek, Drawer, Tabs) to v0. This is exactly at the "miscalibrated" threshold (3+ re-adds). Interpretation: v0's Common 15 under-sampled the navigation/inspection axis (drawer + peek) and the parallel-view axis (tabs); the scaffold surfaced the peek gap concretely (`examples/mini-lead-crm/components/lead-detail-peek.tsx`), and Drawer + Tabs were cheap Base-UI-primitive wraps that didn't justify deferral. For future scope calls, prefer pulling Base-UI-backed components forward even if the ship-cost seems marginal — the cost of a missing primitive shows up when scaffolds start hand-rolling the wrong thing.
