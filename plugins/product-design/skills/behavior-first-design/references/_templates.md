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

## Token template (`tokens/*.md`)

```markdown
# <Token set name>

<Provenance and opt-in status.>

## CSS custom properties

```css
:root {
  --color-...: ...;
  --spacing-...: ...;
}
```

## Tailwind v4 config

```typescript
theme: { ... }
```

## Design decisions captured

- <1-3 bullets: unusual choices and why>
```
