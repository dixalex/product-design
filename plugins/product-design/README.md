# product-design (plugin)

Three sibling skills for product design across Garrett's Elements of UX:

- **`product-architecture`** — Intent → Structure → Flows → Disclosure (4-layer guided dialogue → structural spec)
- **`visual-design`** — Mood → Typography → Color → Spacing → Motion → Polish (6-layer guided dialogue → visual spec + CSS tokens)
- **`behavior-first-design`** — Inputs → Focus & Keyboard → Response-time → Feedback → Motion fire-policy → Component binding (6-layer guided dialogue → behavior spec + Component-binding table)

All three are process skills. Each gates on user approval before handing off. Each produces a *spec* downstream skills consume as upstream truth.

## Pipeline

```
product-architecture → visual-design → behavior-first-design → superpowers:writing-plans → subagent-driven-development → verification → review → finishing
```

3 plugin skills + 6 `superpowers` stages = 9 total. User navigates 3; the rest are upstream/downstream peers. See `PIPELINE.md` at plugin root for the canonical diagram + role lines + exit ramps.

The plugin works without `superpowers` installed (specs are markdown), but the two are designed to compose.

## Example end-to-end run

```
"Design a lead-tracking CRM. Use the product-architecture skill."
"Now run visual-design to write the visual spec."
"Now run behavior-first-design to write the behavior spec."
"Use superpowers:writing-plans to turn the 3 specs into an implementation plan."
"Execute via superpowers:subagent-driven-development."
```

See the marketplace [README](../../README.md) for full install + usage instructions.
