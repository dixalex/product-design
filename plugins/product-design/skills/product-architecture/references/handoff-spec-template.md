# Handoff spec template

The OUTPUT artifact of a `product-architecture` session. Written to `<project-root>/docs/product-architecture/<YYYY-MM-DD>-<project-slug>.md`.

## Template (copy + fill)

```markdown
---
project: "<name>"
date: YYYY-MM-DD
domain: <crm | productivity | dashboard | mobile | dev-tool | design-tool | media | feed | other>
domain_peers: [<peer-1>, <peer-2>, <peer-3>]
intent:
  jtbd: "<When X, I want Y, so I can Z>"
  persona: "<role, scale, context>"
  primary_job: "<the one thing, one-line>"
glossary: <inline | path/to/glossary.md>      # Pocock Ubiquitous Language; locks domain vocabulary; downstream specs consume verbatim
---

# <Project> — product architecture

## Intent

<Prose expansion of JTBD, IDEO 3-lens desirable/feasible/viable read, domain justification. 150–300 words.>

## Structure

### Objects (OOUX)
- **<Object name>** (<primary|container|dependent>) — <key properties; states if the object has a lifecycle>
- ... (3–7 objects)

### Surfaces
- `<SurfaceName>` — <purpose>; primary object: <object>; views: <list | kanban | calendar | ...>
- ... (2–6 surfaces)

### Hierarchy
- Sidebar: <items>
- Top bar: <items>
- Per-surface local actions: <items>

## Flows

<!-- Flows = navigation BETWEEN surfaces. Within-surface state machines, inline-edit timing, keyboard shortcuts inside a list row all belong to behavior-first-design — NOT here. -->

### Primary flows
- **<Flow name>:** <surface> → <surface> → <surface> → <outcome>
- ... (2–4 primary flows)

### Entry points
- `<SurfaceName>`: <entry points — sidebar / URL / cmd-K / search>
- ... (per surface)

### Navigation pattern
<One paragraph describing global chrome — sidebar, topbar, cmd-K, breadcrumbs. Why this pattern over alternatives.>

## Disclosure

### Always visible
<Per primary surface, what's on screen on arrival.>

### One click away (hover / click / peek)
<Per primary surface, what's revealed.>

### Settings-level (2+ clicks)
<Per product, what's buried deep.>

## Handoff to behavior-first-design

Surfaces ready for component generation:

| Surface | Invokes behavior-first-design for |
|---|---|
| <SurfaceName> | <component-1>, <component-2>, <component-3> |
| ... | ... |

Run `behavior-first-design` per surface when ready to scaffold components.

## Principles cited

- **Intent:** JTBD (Christensen), IDEO 3-lens
- **Structure:** Brown's IA principles (<which ones>), OOUX, Polaris one-home-many-doors; cross-ref `behavior-first-design/references/foundations.md § Hick's Law`, `§ Miller's Law`, `§ Jakob's Law`
- **Flows:** Polaris wayfinding, Brown Front Doors; cross-ref `behavior-first-design/references/foundations.md § Peak-End Rule`, `§ Fitts's Law`
- **Disclosure:** Brown Principle of Disclosure, Tesler's Law; cross-ref `behavior-first-design/references/foundations.md § Tesler's Law`, `§ Aesthetic-Usability Effect`

## Glossary (Ubiquitous Language)

Project-specific vocabulary locked at the structural-spec stage. Downstream specs (visual, behavior) consume this verbatim — no synonym drift across the pipeline. Pocock/Evans (DDD) framing: shared vocabulary between human and AI cuts verbosity drift; AI thinks in less verbose, more aligned language when the glossary is explicit.

| Term | Definition | Disambiguation |
|---|---|---|
| <term> | <1-line definition> | <which sibling terms it's NOT> |

Example (Pine IRM):

| Term | Definition | Disambiguation |
|---|---|---|
| Lead | A potential customer record at any pipeline stage | Not the same as Contact (a person) or Account (a company) |
| Activity | A logged event on a Lead (call, email, meeting) | Not the same as Task (an open commitment) |
| Task | An open commitment with a due date | Not the same as Activity (which is past, not future) |
| Inbox | The queue of Tasks due now or overdue | Not the same as Pipeline (the kanban of Lead stages) |

## Decisions log

- Chose <X> over <Y> because <principle / domain-peer reasoning>
- Deferred: <scope-cuts for later>
- Cross-domain surprise offered: <yes/no, which, whether adopted>
```

## Component vocabulary (for the Handoff table)

When filling the Handoff table, use the component names from `behavior-first-design/references/components/`. The v1 inventory is 18 components:

- `button`
- `checkbox`
- `combobox-select`
- `command-palette`
- `datepicker`
- `dialog`
- `drawer`
- `dropdown-menu`
- `empty-state`
- `filter`
- `input-inline-edit`
- `list-row`
- `peek`
- `popover`
- `status-pill`
- `tabs`
- `toast-undo`
- `tooltip`

If the user types a component name that isn't on this list, the skill warns ("component `X` not in behavior-first-design inventory — typo? Or is this a new component that needs a spec?") but does not block.

## Validation at write-time

The skill verifies before writing the spec:

- **YAML front-matter parses** — the `---` fences are balanced and the keys (`project`, `date`, `domain`, `domain_peers`, `intent`) are all present and non-empty.
- **All 4 layer sections present** — exactly one `## Intent`, `## Structure`, `## Flows`, `## Disclosure` heading. Missing or duplicate = fail.
- **Handoff table non-empty** — at least one row under the `## Handoff to behavior-first-design` table. An empty table means the spec produced no surfaces worth component generation, which is a workflow smell — surface it to the user, don't write.
- **Component names in Handoff table match known inventory** — each comma-separated component in the second column matches the 18-item list above. Unknown components generate a warning but do not block the write (users may be piloting new components).
- **Cross-references resolve** — every `behavior-first-design/references/foundations.md § <Law>` reference in the Principles section names a Law that actually exists in that file. Unresolved reference = fail.

If validation fails, the skill surfaces the specific failure and asks the user to revise the relevant section before it writes.

## Why this template shape

The brief is Markdown + YAML because:

- **YAML front-matter is machine-readable.** Downstream agents (behavior-first-design, visual-design) read the front-matter to get the structured fields (domain, peers, JTBD, surfaces) without parsing prose.
- **Markdown body is human.** The designer (or reviewing teammate) reads the prose; the structured sections serve as a table of contents.
- **The Handoff table is the routing layer.** It is the explicit interface between product-architecture and behavior-first-design. A downstream agent or human reviewer reads this table to know which components to generate next.

See `_decisions-ledger.md` decision #9 for the full rationale.
