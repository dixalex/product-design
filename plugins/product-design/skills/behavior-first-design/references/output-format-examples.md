# Behavior spec — output format examples

Examples of the spec artifact produced at spec-write time. Behavior specs follow the §2.2 template defined in `_templates.md § Behavior-spec template`.

The skill writes ONE behavior spec per project surface (or one multi-surface spec if surfaces share enough state). The spec lands at `<project-root>/docs/behavior-first-design/<YYYY-MM-DD>-<slug>.md`.

## Example 1 — minimal CRM behavior spec (truncated)

Excerpt from a sample Pine IRM behavior spec showing the §2.2 template's load-bearing sections:

````markdown
---
project: "Pine IRM"
date: 2026-04-29
domain: crm
inputs:
  - {surface: inbox, primary_modality: keyboard, secondary: pointer, declined: touch}
keyboard:
  global: {⌘K: command-palette, "g i": jump-to-inbox}
  per_surface: {inbox: {J: next-row, K: prev-row, Enter: open-focused}}
response_time:
  - {action: row-select, target: 0ms, optimistic: false}
  - {action: mark-task-done, target: <400ms, optimistic: true}
feedback:
  - {action: mark-task-done, signal: toast+undo, recovery: 5s undo window}
motion_fire_policy:
  frequent_low_novelty: instant
  rare_high_novelty: 150-200ms ease-out
components_used: [list-row, command-palette, toast-undo]
constraints_from_upstream: ["Inbox virtualizes 100k+ rows — row interactions instant"]
canonical_voices_used: [linear]
user_voices: []
---

# Pine IRM — behavior spec

## Inputs
<paragraph + table>

## Focus & Keyboard
<chord map + focus state machine>

## Response-time & Optimism
<budget table + optimistic-UI inventory>

## Feedback & Recovery
<per-action recovery table>

## Motion fire-policy
<frequency-novelty matrix output>

## Component binding
| Surface | Action | Component | Behavior contract |
|---|---|---|---|
| inbox | row | list-row | hover-reveal at 0ms; J/K nav; Enter opens detail |

## Decisions log
<per-layer one-line entries>
````

## Example 2 — what the spec is NOT

The behavior spec does NOT emit React/Next.js/SwiftUI code. Component implementation belongs to `superpowers:writing-plans` + `subagent-driven-development` (or to `frontend-design` for visual surfaces). The spec's `Component binding` table provides the BINDING contract — which component implements which surface-action, and what behavior contract attaches — but not the component source.

```typescript
// NOT in the behavior spec — this is downstream code:
function ListRow({ lead, focused, onSelect }: ListRowProps) {
  // implementation is writing-plans' output
}
```

## Why this format

- **Mirrors visual-design's spec shape** — frontmatter + per-layer H2 sections + decisions log. Three skills, three specs, one consistent shape downstream.
- **YAML frontmatter is queryable** — keyboard chord maps, response-time targets, motion fire-policy all parse cleanly for downstream consumers (writing-plans, frontend-design).
- **Component binding table is the load-bearing artifact** — surface × action rows from product-architecture's Flows handoff; behavior contract attached per row; downstream code-generation knows exactly what to build.

## See also

- `_templates.md § Behavior-spec template` — the OUTPUT template
- `_templates.md § Layer template` — internal template for the 6 dialogue layers
- spec `pine-research/docs/superpowers/specs/2026-04-28-product-design-plugin-v0.4.0-design.md § 2.2` — full template
