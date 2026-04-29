# Execution skills allowlist

Curated list consumed at every plugin-exit gate. Filtered by `fires_at` field, intersected with installed skills, grouped by status. Not surfaced to users: `candidates_unverified` entries.

Status values:
- `always` — fires regardless of spec contents (e.g., always recommend writing-plans after a behavior spec)
- `proven` — plugin author has personally run the spec → execution chain end-to-end with this skill
- `candidates_unverified` — documented for future verification; NOT surfaced to users

Skills auto-demote to `candidates_unverified` after 6 months without `last_verified` refresh.

## Allowlist (v1)

```yaml
- id: superpowers:brainstorming
  source: superpowers/5.0.7
  fires_at: [product-architecture.intent-q1]
  triggers: [bare-prompt-detected]
  reason: "Refine prompt before Intent derivation when JTBD/persona/scope signals are thin."
  last_verified: 2026-04-29
  status: always

- id: superpowers:writing-plans
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [behavior-spec-complete]
  reason: "Convert the 3 specs + plan into per-task implementation."
  last_verified: 2026-04-29
  status: always

- id: superpowers:subagent-driven-development
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [plan-ready]
  reason: "Canonical execution mode for writing-plans output."
  last_verified: 2026-04-29
  status: always

- id: superpowers:test-driven-development
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [plan-ready]
  reason: "Discipline within execution; TDD per task."
  last_verified: 2026-04-29
  status: always

- id: superpowers:verification-before-completion
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [execution-complete]
  reason: "Pre-merge verification gate."
  last_verified: 2026-04-29
  status: always

- id: superpowers:requesting-code-review
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [execution-complete]
  reason: "Code review after implementation."
  last_verified: 2026-04-29
  status: always

- id: superpowers:receiving-code-review
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [review-received]
  reason: "Code-review response discipline."
  last_verified: 2026-04-29
  status: always

- id: superpowers:finishing-a-development-branch
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [review-approved]
  reason: "Close out the development branch."
  last_verified: 2026-04-29
  status: always

- id: superpowers:using-git-worktrees
  source: superpowers/5.0.7
  fires_at: [behavior-first-design.component-binding-gate]
  triggers: [long-running-feature]
  reason: "Isolate long-running feature work in a worktree."
  last_verified: 2026-04-29
  status: always

- id: frontend-design
  source: anthropic-skills
  fires_at: [visual-design.polish-gate, behavior-first-design.component-binding-gate]
  triggers: [tokens-block-present, components-bound]
  reason: "Production UI execution; honors visual spec tokens best-effort. Visual spec is upstream truth, frontend-design implementation is best-effort."
  last_verified: 2026-04-26
  status: proven

# === excluded deliberately ===
# - superpowers:writing-skills (meta, not execution)
# - superpowers:dispatching-parallel-agents (lower-level primitive used by subagent-driven-development)
# - superpowers:systematic-debugging (fires on bug-encountered signal during execution, not at our handoff)
# - superpowers:using-superpowers (framework bootstrap)

# === candidates unverified (NOT surfaced to users) ===
- id: claude-api
  source: anthropic-skills
  fires_at: []
  triggers: []
  reason: "Future candidate; not yet verified end-to-end with our specs."
  last_verified: null
  status: candidates_unverified
```

## Maintenance

- Re-verify each `proven` entry every 6 months by running the spec → execution chain on a real project
- Promote `candidates_unverified` to `proven` after personal verification + update `last_verified`
- Demote `proven` to `candidates_unverified` if `last_verified` > 6 months ago
- Add new entries: paste the YAML schema; fill `fires_at`, `triggers`, `reason`, `status`; mark `last_verified` once personally tested

## Schema

```yaml
- id: <slug>                              # plugin-name OR plugin:skill-name
  source: <plugin/marketplace>            # where the skill comes from
  fires_at: [<skill>.<gate-name>, ...]    # which gate(s) emit this suggestion
  triggers: [<condition>, ...]            # spec-content conditions
  reason: <user-visible 1-line rationale>
  last_verified: YYYY-MM-DD               # null if unverified
  status: always | proven | candidates_unverified
```
