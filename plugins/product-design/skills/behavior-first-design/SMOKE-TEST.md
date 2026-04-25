# Trigger-phrase smoke test — 2026-04-20

Per spec § 7 criterion 2 and plan Task 20.5.

Open a fresh Claude Code session for each prompt. The skill must either auto-activate (visible via tool-use trace / `Skill` invocation) or be the obvious match Claude offers.

Pass threshold: ≥9/10.

| # | Prompt | Skill activated? |
|---|---|---|
| 1 | build a command palette |  |
| 2 | feel like Linear |  |
| 3 | add inline edit to this |  |
| 4 | keyboard navigation |  |
| 5 | make this feel fast |  |
| 6 | optimistic UI |  |
| 7 | focus management |  |
| 8 | doesn't feel native |  |
| 9 | behavior is off |  |
| 10 | build a CRM list row |  |

Pass rate: _ / 10.

## Frontmatter pre-check (automated, 2026-04-20)

All 10 trigger phrases verified present in `SKILL.md` frontmatter description (substring match, YAML line-wrap collapsed):

- ✓ feel like Linear
- ✓ inline edit
- ✓ keyboard navigation
- ✓ make this feel fast
- ✓ optimistic UI
- ✓ focus management
- ✓ doesn't feel native
- ✓ behavior is off
- ✓ build a CRM list row
- ✓ command palette

Phrases with exact plan-verbatim match: 9. Phrase "build a command palette" matches as substring "command palette"; the "build a" prefix is not present verbatim but should be covered by Claude's trigger inference. If prompt 1 fails, add "build a command palette" explicitly to the description.

## Revision policy (per plan Task 20.5 Step 3)

- Pass ≥9/10 → proceed to Task 21 (Alex Phase A gate).
- Fail <9/10 → revise `SKILL.md` frontmatter `description` (keyword density is primary lever). Re-run. Max 2 revision cycles; on 3rd fail, escalate.
