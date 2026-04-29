# Reference file templates (visual-design)

Every file in `references/` follows one of these templates. Keep files ≤2,000 words.

## Link syntax convention (MANDATORY)

Cross-references between skill files use **skill-root-relative paths without the `references/` prefix**:

- `layers/mood.md § canon` (ok)
- `canon/refactoring-ui.md` (ok)
- `references/layers/mood.md` (wrong — redundant)

Cross-references to **sibling skills** in the plugin use plugin-relative form:
- `behavior-first-design/references/foundations.md § Hick's Law` (ok)
- `behavior-first-design/references/motion.md § Frequency-novelty matrix` (ok)
- `product-architecture/references/handoff-spec-template.md` (ok)

Heading anchors use `§ <keyword>` (not markdown `#anchor`). Agents resolve cross-references by `§ <keyword>` lookup.

## Layer template (`layers/*.md` entries)

```markdown
# <Layer name>

<One-line purpose.>

## Canon

<3–6 citations. Each entry: source + 1-line quote or paraphrase.>

## Derivation method

What's the named method this layer's options derive from? E.g., voice-personality mapping for Typography. The method is the antidote to peer-mimicry — without it the dialogue falls back to "Linear's typography / Stripe's typography." Spec § 4 lists the methods per layer; this section makes the chosen method actionable inside the dialogue.

## Required output

What this gate's dialogue produces in the handoff spec. Bullet list of fields. See `handoff-spec-template.md` for shape.

## Dialogue questions

Numbered list of the questions the skill asks, in order. One per skill message; the skill waits for the user's answer before asking the next question.

For each question: (a) the prompt itself; (b) expected answer shape; (c) options the skill proposes (always derivation-based; peer voices cited as precedent AFTER user picks).

## Constraint surfacing (if applicable)

Per spec § 2.5: when the structural spec flags virtualized data, keyboard-primary input, or high-frequency hover-reveals, this layer surfaces those constraints as floors that mood-derived choices adjust within.

## Options pattern

Skill proposes 1–3 derivation-based options, leading with a recommended default ("my lean is X because…"). After the user picks, skill cites canonical voices (Linear / Stripe / Operate / Rauno) as precedent — convention precedent (Jakob's Law), not source.

## User-voice prompt

After peer-comparison, skill asks: *"Any reference beyond the canonical voices? Drop a name or URL."* Captured references queue for research at spec-write time; voice files land project-local at `<project-root>/docs/visual-design/voices/<slug>.md`.

## Principles invoked

Cross-references to `canon/*.md`, `voices/*.md`, and sibling skills' references where applicable.

## Gate criteria

What "done" looks like:
- [ ] <criterion 1>
- [ ] <criterion 2>
- User has confirmed the layer's block in the handoff spec.

## Transition prompt

The verbatim sentence the skill emits to move to the next layer. Form:
"<This layer> captured. Ready to move to <next layer>, or should I revise? (yes / revise)"
```

## Canon template (`canon/*.md` entries)

```markdown
# <Canon name>

- **Author/source:** <Author, year, publication or URL>
- **Statement:** <1-2 sentence canonical form of the principle/framework>
- **Applies to layer:** <Mood | Typography | Color | Spacing | Motion | Polish | cross-cutting>
- **Why it matters:** <2-3 sentences on what this principle earns for a designer>
- **How the skill uses it:** <1-2 sentences on which dialogue question or proposed-option depends on this>

## Full principle(s)

<Longer-form reference content: the actual principles / steps / taxonomy from the source.>

## Citation

<Full citation. Include ISBN / DOI / URL.>
```

## Voice template (`voices/*.md` entries — both ship-with canonical voices AND user-supplied project-local voices)

```markdown
# <Voice name> — <product/designer>

- **Source:** <URL of homepage, brand site, design blog>
- **Captured:** <YYYY-MM-DD>
- **Layer relevance:** <Mood | Typography | Color | Spacing | Motion | Polish — list which layers the voice fires at>

## Aesthetic profile

<2-3 paragraphs: what's distinctive about this product's visual aesthetic? What would you copy? What would you NOT copy?>

## Per-layer treatment

For each layer this voice fires at:

### <Layer>
- <Specific decision the voice makes>
- <Why it works>
- <When NOT to copy>

## Citation

<URL list — homepage, design blog posts, brand site, screenshots if archived.>
```

## Handoff spec template (`handoff-spec-template.md`)

The OUTPUT artifact template the skill writes at spec-write time. See that file for full shape — it's structurally unique and not a template-template.

## Derivation guidance

Per layer, the derivation method needs explicit guidance the skill applies during the dialogue. The detailed guidance per layer lives in the per-layer files (`layers/*.md § Derivation method`) — colocating it next to that layer's dialogue questions, gate criteria, and constraint surfacing keeps related concerns together.

This template's job is to **require** the `## Derivation method` section in every layer file. Layer files MUST contain:

- The named derivation method for the layer (per spec § 4)
- The signals it consumes (e.g., Mood: JTBD's emotional verbs + persona's tool-adjacency + scope-as-feature exclusions)
- A worked example showing how the signals produce options
- Anti-pattern: what the layer falls into if derivation is skipped (e.g., peer-mimicry: "Linear's typography because Linear")

Cross-reference: spec `2026-04-26-visual-design-skill.md § 4` lists the canonical derivation method per layer.
