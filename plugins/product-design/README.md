# product-design (plugin)

Three sibling skills for product design across Garrett's Elements of UX:

- **`product-architecture`** — Intent → Structure → Flows → Disclosure (process; guided dialogue)
- **`visual-design`** — Mood → Typography → Color → Spacing → Motion → Polish (process; guided dialogue; v0.3.0)
- **`behavior-first-design`** — Skeleton-layer component reference library + 6-step output contract

## Pipeline

`product-architecture` → `visual-design` → `behavior-first-design` (→ optional `frontend-design` for execution polish).

Each upstream skill produces a brief that downstream skills consume as upstream truth. Briefs are markdown + YAML + (where relevant) CSS tokens — committed to the project repo as design records.

## Example

```
"Design a lead-tracking CRM. Use the product-architecture skill."
"Now run visual-design to write the visual brief."
"Scaffold the components into examples/crm/. Use behavior-first-design."
```

See the marketplace [README](../../README.md) for full install + usage instructions.
