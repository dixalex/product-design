# product-design (plugin)

Three sibling skills for product design across Garrett's Elements of UX:

- **`product-architecture`** — Intent → Structure → Flows → Disclosure (process; guided dialogue → structural spec)
- **`visual-design`** — Mood → Typography → Color → Spacing → Motion → Polish (process; guided dialogue → visual spec with CSS tokens)
- **`behavior-first-design`** — Inputs → Focus & Keyboard → Response-time & Optimism → Feedback & Recovery → Motion fire-policy → Component binding (process; guided dialogue → behavior spec; v0.4.0)

## Pipeline

`product-architecture` → `visual-design` → `behavior-first-design` → `superpowers:writing-plans` → `subagent-driven-development` → ...

Each upstream skill produces a *spec* that downstream skills consume as upstream truth. Specs are markdown + YAML + (where relevant) CSS tokens — committed to the project repo as design records.

Three plugin skills + six superpowers stages = 9 total stages. User navigates the 3 plugin skills; the rest are upstream/downstream peers. See `PIPELINE.md` at plugin root for the canonical diagram.

## Example

```
"Design a lead-tracking CRM. Use the product-architecture skill."
"Now run visual-design to write the visual spec."
"Now run behavior-first-design to write the behavior spec."
"superpowers:writing-plans now turns the 3 specs into an implementation plan."
```

See the marketplace [README](../../README.md) for full install + usage instructions.
