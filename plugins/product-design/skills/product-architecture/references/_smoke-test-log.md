# Smoke test log

## 2026-04-18 — v1 structural + sample-brief test

- 14 reference files all present: yes
- 6 cross-anchors to foundations.md resolve: yes (Hick, Miller, Jakob, Tesler, Peak-End, Fitts)
- Total word count: 11,639 (budget: ≤25,000)
- Component inventory drift: none (template vocab matches `behavior-first-design/references/components/` exactly — 18-for-18)
- Anchor hygiene in layer files: verified — `structure.md`, `flows.md`, `disclosure.md` all cite `behavior-first-design/references/foundations.md § <Law>` format consistently
- Sample brief produced at `/tmp/test-brief.md` (hypothetical Pine IRM, CRM domain, 5 objects, 4 surfaces, 3 primary flows), passed all validations:
  - YAML front-matter parses (pyyaml): `project`, `date`, `domain`, `domain_peers`, `intent` all present and non-empty
  - All 4 layer H2 sections (`## Intent`, `## Structure`, `## Flows`, `## Disclosure`) present, exactly one each
  - Handoff table has 4 rows referencing 14 distinct components — all real names from the inventory (button, combobox-select, command-palette, dialog, drawer, dropdown-menu, empty-state, filter, input-inline-edit, list-row, peek, status-pill, tabs, toast-undo)
- Gaps flagged: none

## Notes for v2

- Sample brief is hypothetical, not a real Pine IRM spec — it exercises the template but should not be read as a product commitment.
- Component inventory is pinned at 18. When behavior-first-design adds components, re-run the inventory drift check (Task 23 Step 5) and update `handoff-spec-template.md § Component vocabulary`.
- Word budget has 13K headroom. If v1.x docs grow, the budget is the forcing function for tightening.
