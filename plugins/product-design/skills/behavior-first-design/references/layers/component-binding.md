# Component binding

Bind Common-18 components to surface-actions; attach behavior contracts; emit `components_used:` frontmatter + binding table.

## Canon

- `components/*.md` (Common 18) — the primary and fixed canon for this layer. Every component selection must resolve to one of the 18 IDs in this directory. No other component source is authoritative.
- `patterns/*.md` — cross-component meta-rules (error-surface, form-validation-timing, multi-select, real-time-indicators, skeleton-vs-spinner). These are not Common-18 components; they are policies that apply across multiple bindings. Consulted per row as the contract is assembled, not as component alternatives.
- OOUX Prater § CTAs — consumed from product-architecture's Flows handoff, carried through the structural spec. The CTA column on each surface-action row names what the user is doing to what noun; Layer 6 maps that verb-noun pair onto a Common-18 component.
- Garrett § Interface Design (verb-on-noun bindings) — governs which component class handles the interaction. Non-overlapping scope with Flows' Navigation Design: Garrett here resolves the binding decision (e.g., inline-edit vs. dialog) for data interactions; Navigation Design was resolved upstream in product-architecture.

## Derivation method

**Named method: surface-action × component matrix.**

Rows come from the structural spec's Flows handoff. Each row is a `(surface, action verb)` pair. For each pair, the skill picks one component from Common 18 and assembles a behavior contract stitched from the committed prior layers.

**Step 1 — Extract surface-action rows from structural spec's Flows.** Every named action on every named surface becomes a row. No surface may be omitted.

**Step 2 — Select component from Common 18.** Match the action verb and surface context to the closest Common-18 ID. The authoritative Common-18 list (v0.4.0, fixed) is:

`button`, `checkbox`, `combobox-select`, `command-palette`, `datepicker`, `dialog`, `drawer`, `dropdown-menu`, `empty-state`, `filter`, `input-inline-edit`, `list-row`, `peek`, `popover`, `status-pill`, `tabs`, `toast-undo`, `tooltip`

No invented names. If a surface action has no clear match, surface it as a v0.5+ candidate — do not extend the list.

**Step 3 — Attach behavior contract.** For each row, assemble the contract from the prior layer outputs:

- Keyboard + focus (Layer 2): chord, tab order, focus-trap contract if applicable
- Response-time (Layer 3): tier and optimistic flag
- Feedback (Layer 4): signal, recovery mechanism, validation timing
- Motion (Layer 5): fire-policy cell (instant / animate / animate-despite-frequency)

**Step 4 — Cross-pattern check.** Apply relevant `patterns/*.md` entries to the contract column. `form-validation-timing` for form inputs; `multi-select` for list selections. Patterns annotate the contract — they do not replace the component.

**Worked example — Pine IRM:**

| Surface | Action | Component | Behavior contract |
|---|---|---|---|
| inbox | row | `list-row` | hover-reveal action chips at 0ms (Layer 5 Cell 1); J/K nav (Layer 2); Enter opens detail (Layer 2); 0ms motion (Layer 5) |
| inbox | mark-done | `toast-undo` | optimistic UI (Layer 3); 5s undo window (Layer 4); 150ms toast enter (Layer 5 Cell 2) |
| inbox | command | `command-palette` | ⌘K opens (Layer 2); 200ms enter (Layer 5 Cell 2) |
| lead-detail | edit | `input-inline-edit` | click-to-edit (Layer 2); on-blur saves (Layer 4); Escape cancels (Layer 4); 0ms motion (Layer 5) |
| lead-detail | archive | `dialog` | confirm pattern (Layer 4); irreversible; Cancel + Esc both close; focus trap (Layer 2) |
| lead-detail | status | `status-pill` | icon + text label (Layer 4 a11y); no color-alone signal |

**Anti-pattern: inventing component names.** "kbd-chip", "header-bar", "side-panel" are not in Common 18. Invented names produce unbuildable specs. Log any unmatched action as a v0.5+ candidate in `## Candidates` — do not add it to the binding table.

## Required output

Three artifacts in the handoff spec:

- `components_used:` frontmatter array — Common-18 IDs only. No invented names.
- `## Component binding` table — columns: Surface / Action / Component / Behavior contract. One row per surface-action pair from the structural spec's Flows. Contract column cites Layer 2–5 inline.
- Cross-references in spec body — one sentence per prior layer noting where its outputs appear in the binding table.

## Dialogue questions

Three questions. The skill waits for the user's answer before advancing.

**Question 1 — Per-row binding proposal.** The skill walks each surface-action row from the Flows handoff:

> "Surface `<name>` action `<verb>`: I see this in your structural spec. My lean: bind to `<component_id>` from Common 18 because <rationale>. Pick or override."

Batched by surface when several actions share a component class. After confirmation: "Linear's inbox uses `list-row` + `toast-undo` + `command-palette` as the canonical trio for triage-model surfaces."

**Question 2 — Invented-component audit.** After all rows are proposed, the skill checks the complete table for any component ID not in Common 18:

> "All components must be from Common 18 (button, checkbox, combobox-select, command-palette, datepicker, dialog, drawer, dropdown-menu, empty-state, filter, input-inline-edit, list-row, peek, popover, status-pill, tabs, toast-undo, tooltip). Inventing: `<list>`. Replace with `<list>` (closest Common-18 match)?"

If no inventions: "All bindings resolve to Common 18. No candidates flagged."

**Question 3 — Behavior contract stitching.** The skill reads each upstream layer's frontmatter and assembles per-row contracts, then presents the complete table:

> "Contracts assembled from Layers 1–5 frontmatter. Any contract row to revise before I write the binding block?"

## Constraint surfacing

Before Question 1, surface two carry-forward constraints:

- **Common-18 list is fixed at v0.4.0.** Unmatched surface-actions go to `## Candidates` as v0.5+ candidates — not into the binding table. State this before derivation begins so the user does not anchor on invented names.
- **Cross-component patterns are meta-rules, not components.** `patterns/*.md` entries (error-surface, form-validation-timing, multi-select, real-time-indicators, skeleton-vs-spinner) annotate the contract column. They do not appear as component IDs in `components_used:` or the Component column.

## Options pattern

Derivation-based first. The skill cites the verb-noun pair and the Garrett/Prater reasoning that produced each binding before any peer reference appears. After confirmation: "Linear's component-binding for inbox-row uses `list-row` + `toast-undo` + `command-palette` — the same Common-18 trio for a keyboard-primary triage surface." Derivation stays primary; precedent is corroboration. No creative naming at this layer — the list is fixed.

## User-voice prompt

At the end of the Component binding layer, after Question 3:

> "Any component reference beyond Common 18 + canonical voices? Drop a name (will be flagged for v0.5+ candidate list, not added to Common 18)."

Captured names queue as candidates — do NOT add to the binding table or Common-18 list. Populate `v0.5_candidates:` frontmatter and `## Candidates` in the spec.

## Principles invoked

`components/*.md` (Common-18 authority; verb-noun binding contracts), `patterns/*.md` (cross-component meta-rules; applied in contract column), OOUX Prater § CTAs (surface-action rows inherited from product-architecture Flows), Garrett § Interface Design (verb-on-noun binding decisions; non-overlap with Flows' Navigation Design).

## Gate criteria

- [ ] Every surface-action row from structural spec's Flows has a binding (no surface omitted)
- [ ] All component IDs in the `components_used:` array are from Common 18 (no inventions; any non-matches logged as v0.5+ candidates)
- [ ] Behavior contracts attached per row with explicit Layer 1–5 references in the contract column
- [ ] User has confirmed the layer's block in the handoff spec

## Transition prompt

```
Component binding captured. (Layer 6 of 6 in behavior-first-design; skill 3 of 3 in product-design pipeline.)
Ready to write the behavior spec, or should I revise? (yes / revise)
```
