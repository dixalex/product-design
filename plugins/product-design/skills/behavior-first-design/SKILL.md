---
name: behavior-first-design
description: Behavior-first design for web UI components — keyboard, focus, response-time, motion, inline editing, optimistic UI. Generates production code for the Common 18 components (Button through Tabs) using Base UI primary + react-aria / cmdk / hand-rolled fallbacks. Use when the user says "build a list row / inline edit / command palette / toast", "keyboard navigation", "should feel like Linear", "make this feel fast", or names a specific component. NOT for whole-product information architecture (use product-architecture).
---

# Behavior-First Design

## Why this skill exists

Behavior-first means keyboard, focus, response-time, motion semantics, and state transitions are specified BEFORE visual layout or styling. This skill encodes those decisions for CRM-style web UI — the layer that makes Linear feel like Linear, Superhuman feel like Superhuman, and an arbitrary React scaffold feel like neither.

Most UI bugs in CRM-style apps aren't visual; they're behavioral. A row that doesn't respond to `j/k`, a dialog that doesn't return focus, a mutation that blocks on a spinner, a destructive action that demands a modal confirmation — each one is individually survivable, collectively fatal to the "feels fast, feels native" quality bar. This skill turns those decisions into a recognizable pattern library so Claude doesn't reinvent them on every component.

## What this skill is, cognitively

This skill is a **pattern library + mental-model scaffolding** Claude applies via **Recognition-Primed Decision** (Klein 1998, *Sources of Power*; Kahneman & Klein 2009, *Conditions for Intuitive Expertise*): on a UI task, Claude recognizes the pattern (*"this is an inline-edit input"*), consults the matching component reference, and applies — without re-deriving from first principles. The Common 18 is the prototype library; the 8 principles are the mental models. When cues don't match cleanly (novel component, edge case), the skill falls back to System-2 deliberation via the 4 clarifying questions + self-audit.

## The 8 principles

### Principle 1 — Response is instant (0–100ms) or it isn't

- **Statement:** Any UI response to direct input must land inside 100ms, or it must be acknowledged as asynchronous and handled with optimistic UI.
- **Do:** Apply the change locally first (optimistic UI), then reconcile with the server; use targets in `response-time-budget.md`.
- **Don't:** Show a spinner for local state changes that don't cross the network.
- **Applies to:** All components. (Nielsen 1993, Doherty 1982.)

### Principle 2 — Keyboard is a first-class input, not a fallback

- **Statement:** Every action that can be taken with a mouse must also be reachable and invocable from the keyboard, on equal footing.
- **Do:** Wire every action to a named key or chord; document it; surface it in the command palette and tooltips.
- **Don't:** Hide features behind hover menus, drag handles, or right-click-only affordances.
- **Applies to:** All interactive components. (Primer, Linear, Raycast.)

### Principle 3 — Focus is a cursor you manage deliberately

- **Statement:** Focus is application state: move it intentionally toward the user's next action, and return it intentionally when context closes.
- **Do:** Move focus to freshly revealed content (dialog, inline editor, new row), and restore focus to the trigger when that content closes.
- **Don't:** Yank focus away while the user is typing, scrolling, or working somewhere else on the page.
- **Applies to:** Navigation, dialog, create/delete flows, any open/close component. (Primer, Atlassian, Polaris.)

### Principle 4 — Optimistic UI beats spinners

- **Statement:** For mutations expected to complete in under 2 seconds, update the UI immediately and reconcile with the server asynchronously.
- **Do:** Apply the change locally, show a subtle in-flight indicator if needed, and reconcile/rollback on server response.
- **Don't:** Block the UI with a spinner or disable the affected controls while waiting on the network.
- **Applies to:** Any mutation whose expected round-trip is under ~2s. (TanStack Query, Linear, Superhuman.)

### Principle 5 — Motion is semantic, never decorative

- **Statement:** Animate only to communicate a state change or spatial continuity; never to add visual interest.
- **Do:** Animate enter/exit for new context (dialog, popover, toast); use curves and durations from `motion.md`.
- **Don't:** Animate frequent, user-initiated actions (row toggles, field focus, filter changes).
- **Applies to:** All state transitions; all animation decisions. (Rauno, Apple HIG motion.)

### Principle 6 — Affordances appear on hover, not in idle state

- **Statement:** Secondary and tertiary actions on dense surfaces reveal themselves on pointer intent; the idle state stays quiet.
- **Do:** Reveal row actions on `pointerenter` / focus-within; keep the idle state reserved for primary identity and status.
- **Don't:** Clutter the idle row with hover-only actions baked into every render.
- **Applies to:** List rows, table cells, dense grid surfaces. (Linear density doctrine.)

### Principle 7 — Inline editing beats modal editing

- **Statement:** Single-field edits happen where the value already lives; modals are reserved for multi-field, multi-step, or destructive flows.
- **Do:** Click / Enter to edit, blur / Enter to save, Escape to cancel; persist optimistically.
- **Don't:** Open a dialog to change a single title, status, or owner field.
- **Applies to:** Text fields, single-value edits on rows and detail panels. (Notion, Linear.)

### Principle 8 — Undo is free; confirm is expensive

- **Statement:** For reversible destructive actions, act immediately and offer undo; reserve confirm modals for irreversible or high-blast-radius actions.
- **Do:** Execute the destructive action, surface a toast with an undo affordance for N seconds, and fully reverse on click.
- **Don't:** Wrap reversible operations (archive, delete-with-trash, bulk status change) in a confirm modal.
- **Applies to:** Reversible destructive actions. (Shopify Polaris, Peak-End rule.)

See `foundations.md § <Law>` for full citations and `voices/*.md` for product-specific authority.

## Conflict-resolution doctrine (always loaded)

- **Motion:** Rauno + Linear > HIG + Material. Don't animate frequent actions. HIG prescribes motion for state continuity; ours restrains based on frequency/novelty. See `motion.md`.
- **Response time:** Stricter target wins in critical flows — Superhuman 50ms in critical paths; Nielsen 100ms is outer bound. See `response-time-budget.md`.
- **Accessibility always > motion.** `prefers-reduced-motion` unconditional.
- **Evidence density:** Foundational laws cite per occurrence; voices cite per quote; component rules attributed at section header.

## Reference index

Load per the hard trigger rules below.

| Topic | File |
|---|---|
| 10 foundational laws | `references/foundations.md` |
| Numeric response-time targets | `references/response-time-budget.md` |
| Keyboard and focus | `references/keyboard-and-focus.md` |
| Motion rules | `references/motion.md` |
| Anti-patterns | `references/anti-patterns.md` |
| Past decisions | `references/_decisions-ledger.md` |
| Per-file templates | `references/_templates.md` |
| Components (Common 18) | `references/components/*.md` |
| Cross-cutting patterns | `references/patterns/*.md` |
| Product voices | `references/voices/*.md` |
| Tokens (optional) | `references/tokens/*.md` |
| Stack binding | `references/stack-bindings/web-primitives.md` |
| Worked examples | `references/output-format-examples.md` |
| v2 gaps | `references/_v2-gaps.md` |

## The 4 clarifying questions (answered before generating)

1. **What component/pattern is this?** (name primitive + any composition)
2. **Keyboard-first or mouse-first primary usage?**
3. **Which of the Common 15 primitives compose this?**
4. **Stack constraint?** (Base UI / react-aria / cmdk / hand-rolled)

## Output format (6-step contract)

Every invocation produces output in this exact shape:

1. **Clarifying answers** — answer the 4 questions explicitly
2. **Component checklist** — YAML from the component's reference file, filled
3. **Citations** — `// per <source>: <statement>` — ≥1 foundational + ≥1 voice
4. **Code** — the component
5. **Behavior contract** — 3–6 bullets: keyboard, focus, response-time target, motion, error state
6. **Self-audit** — the "Likely missed" 3-question critique

Worked examples: see `output-format-examples.md`.

## Hard trigger rules (load these references before generating)

- Any input/text-field → `keyboard-and-focus.md` + `components/input-inline-edit.md` + `patterns/form-validation-timing.md`
- Any list or row → `components/list-row.md` + `patterns/multi-select.md`
- Any empty/loading state → `voices/eagle.md § 21` + `patterns/skeleton-vs-spinner.md`
- Any form with errors → `patterns/error-surface.md`
- Any async mutation → `patterns/real-time-indicators.md` + principle 4 (optimistic UI)
- Any animation or transition → `motion.md`
- Any destructive action → principle 8 + `components/toast-undo.md`

## Self-audit template (after code emission)

```
Likely missed:
(a) State I may have skipped: <empty / loading / error / disabled / focus-visible / icon-only-labeled / ...> — [N/A | fixed above | unresolved]
(b) Keyboard shortcut not wired: <key / action pair> — [N/A | fixed above | unresolved]
(c) Empty / loading / error state still TODO: <name> — [N/A | fixed above | unresolved]
```

Any "unresolved" flag → fix in output OR explicitly surface to user before declaring done.

## When NOT to invoke this skill

- SwiftUI / UIKit / iOS / macOS code → use `hig-patterns`, `hig-inputs`, `swiftui-pro` instead
- Pure visual styling without behavior concerns → use `frontend-design`
- Backend / API / data model design → use upcoming `product-architecture` skill
- Animation-as-motion-graphics (not UI state) → not this skill's scope

## Related skills

- `hig-patterns` / `hig-inputs` — Apple HIG (cited as authority source; produces SwiftUI/UIKit output so not composed)
- `frontend-design` — visual/aesthetic layer (complementary)
- `skill-creator` — if editing/extending this skill
- `product-architecture` (upcoming) — data model / architecture sibling
