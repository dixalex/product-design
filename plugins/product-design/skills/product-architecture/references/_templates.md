# Reference file templates (product-architecture)

Every file in `references/` follows one of these templates. Keep files ≤2,000 words.

## Link syntax convention (MANDATORY across all skill files)

Cross-references between skill files use **skill-root-relative paths without the `references/` prefix**:

- `layers/intent.md § canon` (ok)
- `canon/brown-8-ia-principles.md` (ok)
- `references/layers/intent.md` (wrong — redundant; all files live under `references/`)
- absolute paths (`/Users/...`) (wrong — breaks on other machines)

Cross-references to the **sibling skill** `behavior-first-design` use plugin-relative form:
- `behavior-first-design/references/foundations.md § Hick's Law` (ok)

Heading anchors use `§ <keyword>` (not markdown `#anchor`). The `§` form is the convention; agents reading the skill look for it when resolving cross-references.

## Layer template (`layers/*.md` entries)

```markdown
# <Layer name>

<One-line purpose.>

## Canon

<3-6 citations: Author/source, year, URL or reference. Each with 1-line quote or paraphrase that captures the principle.>

## Required output

What this gate's dialogue produces in the handoff brief. Bullet list of fields. See `handoff-brief-template.md` for shape.

## Dialogue questions

Numbered list of the questions the skill asks, in order.
Each question: (a) the question itself; (b) expected answer shape; (c) if multiple-choice, the options to propose.

The skill asks ONE question per message, waits for the user's answer, then moves to the next. Do not batch questions.

## Options pattern

Template for how the skill proposes options at this layer:
- Option A: <from domain peer 1>
- Option B: <from domain peer 2>
- Option C: <from domain peer 3 or user override>
- Optional cross-domain surprise: <what kind of pattern surfaces here>

## Principles invoked

Cross-references to `canon/*.md` and `behavior-first-design/references/foundations.md` where applicable.

## Gate criteria

What "done" looks like. The skill does NOT proceed to the next layer until:
- [ ] <criterion 1>
- [ ] <criterion 2>
- User has confirmed the layer's block in the handoff brief.

## Transition prompt

The verbatim sentence the skill emits to move to the next layer. Always of the form:
"<This layer> captured. Ready to move to <next layer>, or should I revise? (yes / revise)"
```

## Canon template (`canon/*.md` entries)

```markdown
# <Canon name>

- **Author/source:** <Author, year, publication or URL>
- **Statement:** <1-2 sentence canonical form of the principle/framework>
- **Applies to layer:** <Intent | Structure | Flows | Disclosure | cross-cutting>
- **Why it matters:** <2-3 sentences on what this principle earns for a designer>
- **How the skill uses it:** <1-2 sentences on which dialogue question or proposed-option depends on this>

## Full principle(s)

<Longer-form reference content: the actual principles / steps / taxonomy from the source.>

## Citation

<Full citation for scholarly/standard attribution. Include DOI / ISBN / URL.>
```

## Handoff brief template (`handoff-brief-template.md`)

The OUTPUT artifact template the skill writes. See that file for the full shape; it's structurally unique (not a template-template) and does not follow either of the forms above.
