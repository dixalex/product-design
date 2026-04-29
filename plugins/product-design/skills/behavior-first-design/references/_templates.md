# Reference file templates

Every file in `references/` follows one of these templates. Keep files ≤2,000 words.

## Link syntax convention (MANDATORY across all skill files)

Cross-references between skill files use **skill-root-relative paths without the `references/` prefix**:

- ✅ `components/dropdown-menu.md` (preferred)
- ✅ `patterns/form-validation-timing.md`
- ✅ `voices/rauno-mechanics.md § when to load`
- ❌ `references/components/dropdown-menu.md` (redundant — all files are under `references/`)
- ❌ `skills/behavior-first-design/references/components/...` (too verbose)
- ❌ absolute paths (`/Users/...`) — breaks on other machines

For external references:
- Spec file: `pine-research/docs/superpowers/specs/2026-04-17-behavior-first-design-skill.md`
- Eagle full file: `pine-research/design/eagle-product-principles.md`
- Research corpus: `pine-research/.firecrawl/scratch/<filename>`

For heading anchors: use `§ <keyword>` (not markdown `#anchor`) because most reference files use prose over headings.

## Law template (`foundations.md` entries)

```markdown
### <Law name>

- **Source:** <Author, Year, Publication>
- **Statement:** <1-sentence canonical form>
- **CRM implication:** <1-2 sentences — how it affects a Linear-style product>
- **Used by principle:** <which of the 8 principles in SKILL.md invokes this>
```

## Component template (`components/*.md`)

```markdown
# <Component name>

<One-line purpose.>

## Checklist (filled before generating code)

```yaml
disabled_state: <yes|no|n-a>
loading_state: <yes|no|n-a>
focus_visible_only_on_keyboard: <yes|no>
icon_only_has_accessible_name: <yes|no|n-a>
hover_affordance_hidden_in_idle: <yes|no|n-a>
keyboard_activation: <Enter|Space|both>
esc_closes: <yes|no|n-a>
validation_timing: <on-blur|on-change|on-submit|n-a>
<component-specific fields as relevant>
```

## Keyboard contract

<Table: Key | When | Effect — reproduced verbatim from Radix/react-aria with attribution>

## Behavior invariants

- <3-6 bullets distilled from react-aria `useXxx` Features list>

## State transitions

<State machine: idle → focused → active → ... with triggers>

## Stack binding (Base UI)

<1-3 sentences: how to wire in Base UI. Link to `stack-bindings/web-primitives.md` for library fallbacks.>

## Common mistakes

- <2-3 bullets from `anti-patterns.md` relevant to this component>

## Principles invoked

<Backlinks to the 8 principles in SKILL.md this component depends on>

## Sources
- <citations>
```

## Pattern template (`patterns/*.md`)

```markdown
# <Pattern name>

<Decision rule in one paragraph.>

## Decision table

| Condition | Rule | Example |
|---|---|---|

## Rationale

<Cite foundational laws and/or voices.>

## Applies to components

<List the Common 15 components that invoke this pattern.>
```

## Voice template (`voices/*.md`)

```markdown
# <Voice: Linear / Superhuman / Rauno / Eagle>

## Source
<URL or spec-file reference>

## Core quotes (verbatim, attributed)

> <quote 1>
>
> <quote 2>

## Actionable principles

1. <Principle — how a CRM component should behave per this voice>
2. ...

## When to load this voice

<List trigger conditions — e.g., "empty state generation", "motion decision", "when user says 'feel like Linear'">
```

## Token template (REMOVED at v0.3.0)

Tokens are owned by `visual-design` (sibling skill in this plugin). See `visual-design/references/_templates.md § Token template` for the template; see `visual-design/references/tokens/operate-extracted.md` for the canonical example.

## Layer template (`layers/*.md` entries — mirror of visual-design's)

```markdown
# <Layer name>

<One-line purpose.>

## Canon

<3–6 citations. Each entry: source + 1-line quote or paraphrase.>

## Derivation method

What's the named method this layer's options derive from?

## Required output

What this gate's dialogue produces in the handoff spec. Bullet list of fields.

## Dialogue questions

Numbered list of questions the skill asks, one per skill message.

## Constraint surfacing (if applicable)

When the structural/visual specs flag relevant floors.

## Options pattern

Skill proposes 1–3 derivation-based options; peer voices cited as precedent AFTER user picks.

## User-voice prompt

After peer-comparison, skill asks for additional references.

## Principles invoked

Cross-references to canon and sibling skills' references.

## Gate criteria

What "done" looks like (checkbox list).

## Transition prompt

Verbatim sentence the skill emits to move to the next layer.
```

## Component-binding template (Layer 6 output)

```markdown
| Surface | Action | Component | Behavior contract |
|---|---|---|---|
| <surface-name from structural spec> | <action verb> | <Common-18 ID> | <behavior policies attached: keyboard / focus / motion / recovery> |
```

Required: every surface-action row from product-architecture's Flows handoff appears as a row here. No surface omitted; no component invented (must be from Common 18: button, checkbox, combobox-select, command-palette, datepicker, dialog, drawer, dropdown-menu, empty-state, filter, input-inline-edit, list-row, peek, popover, status-pill, tabs, toast-undo, tooltip).

## Focus-state-machine template (Layer 2 per-surface)

```markdown
### <Surface or component>

- **Trigger:** <event that opens/activates>
- **Trap:** <focus target inside the trapped region>
- **Restore:** <where focus returns when closing>
- **Escape:** <Escape key behavior>
```

Required for every surface where focus traps (dialogs, peeks, popovers, command palette).

## Response-budget template (Layer 3)

```markdown
| Action | Target (ms) | Optimistic UI | Tier rationale |
|---|---|---|---|
| <action name> | <0 / <100 / <400 / <1000 / <10000> | <yes/no> | <Nielsen 1993 perception tier; Doherty if 400ms> |
```

Required: every interactive action in the structural spec's Flows has a row.

## Feedback-decision-tree template (Layer 4)

```markdown
| Action | Reversibility | Signal | Recovery | Validation timing |
|---|---|---|---|---|
| <name> | reversible / irreversible / long-running | toast+undo / confirm / progress / inline-validation | <recovery mechanism> | on-blur / on-change / on-submit |
```

Required: every mutation in the structural spec has a row. Reversibility derives the signal: reversible → toast+undo (default 5s window); irreversible → confirm dialog; long-running → progress indicator.
