---
name: behavior-first-design
description: >-
  Behavior-first design principles for web UI, encoding Linear, Operate, Superhuman,
  and Apple HIG interaction patterns. Use when building web UI components, pages, or
  layouts — especially CRM-style apps. Covers keyboard navigation, focus management,
  response-time budgets, motion restraint, inline editing, optimistic UI, command
  palettes, and the Common 15 components. Also use when the user says "make this feel
  fast", "should feel like Linear", "keyboard navigation", "inline edit", "command
  palette", "optimistic UI", "focus management", "behavior is off", "doesn't feel
  native", "build a CRM list row". Stack: Base UI primary + react-aria/cmdk/hand-rolled
  fallback (see stack-bindings/web-primitives.md).
---

# Behavior-First Design

## Why this skill exists

Behavior-first means keyboard, focus, response-time, motion semantics, and state transitions are specified BEFORE visual layout or styling. This skill encodes those decisions for CRM-style web UI — the layer that makes Linear feel like Linear, Superhuman feel like Superhuman, and an arbitrary React scaffold feel like neither.

Most UI bugs in CRM-style apps aren't visual; they're behavioral. A row that doesn't respond to `j/k`, a dialog that doesn't return focus, a mutation that blocks on a spinner, a destructive action that demands a modal confirmation — each one is individually survivable, collectively fatal to the "feels fast, feels native" quality bar. This skill turns those decisions into a recognizable pattern library so Claude doesn't reinvent them on every component.

## What this skill is, cognitively

This skill is a **pattern library + mental-model scaffolding** Claude applies via **Recognition-Primed Decision** (Klein 1998, *Sources of Power*). When invoked on a UI task, Claude recognizes the pattern (*"this is an inline-edit input"*), consults the corresponding component reference, and applies — without re-deriving from first principles. The Common 15 IS the prototype library. The 8 principles ARE the mental models that compress UI complexity so Claude doesn't re-derive each decision.

Recognition works when the cues in the invocation map cleanly to the prototype library. When they don't (novel component, genuine edge case), the skill degrades to System 2 deliberation — the 4 clarifying questions + self-audit exist specifically for that fallback path. This matches Kahneman & Klein (2009) *"Conditions for Intuitive Expertise"*: valid cues + rapid feedback + learnable patterns enable System-1 expert judgment. Absence of any one degrades the skill.

Practical implication: adding components = adding prototypes Claude can recognize. Adding principles = adding mental models. Adding voices = adding aesthetic/judgment reference points. All three compound.

## The 8 principles

### Principle 1 — Response is instant (0–100ms) or it isn't

- **Statement:** Any UI response to direct input must land inside 100ms, or it must be acknowledged as asynchronous and handled with optimistic UI.
- **Source:** Nielsen 1993; see `foundations.md § Nielsen's Response Time Triad` and `foundations.md § Doherty Threshold`.
- **Why:** Below ~100ms, users perceive cause-and-effect; above it, they perceive waiting — and CRM power users feel the difference on every row toggle, status change, and filter pill.
- **Do:** Apply the change locally first (optimistic UI), then reconcile with the server; use the response-time budget targets in `response-time-budget.md`.
- **Don't:** Show a spinner for local state changes that don't cross the network.
- **Applies to:** All components.

### Principle 2 — Keyboard is a first-class input, not a fallback

- **Statement:** Every action that can be taken with a mouse must also be reachable and invocable from the keyboard, on equal footing.
- **Source:** GitHub Primer, Linear, Raycast; see `voices/linear.md`, `voices/superhuman.md`, and `keyboard-and-focus.md`.
- **Why:** Keyboard-driven flows are how CRM users actually work at volume — triage an inbox, advance a pipeline, move across a list — and mouse-only features silently exclude the power path.
- **Do:** Wire every action to a named key or chord; document it; surface it in the command palette and tooltips.
- **Don't:** Hide features behind hover menus, drag handles, or right-click-only affordances.
- **Applies to:** All interactive components.

### Principle 3 — Focus is a cursor you manage deliberately

- **Statement:** Focus is application state: move it intentionally toward the user's next action, and return it intentionally when context closes.
- **Source:** Primer, Atlassian, Polaris; see `keyboard-and-focus.md § When NOT to move focus (Polaris doctrine — critical)`.
- **Why:** Lost or unexpectedly moved focus breaks keyboard flow, screen-reader flow, and the user's sense of place — all three are non-negotiable in a CRM where users tab through dozens of elements per task.
- **Do:** Move focus to freshly revealed content (dialog, inline editor, new row), and restore focus to the trigger when that content closes.
- **Don't:** Yank focus away while the user is typing, scrolling, or working somewhere else on the page.
- **Applies to:** Navigation, dialog, create/delete flows, any component that opens or closes.

### Principle 4 — Optimistic UI beats spinners

- **Statement:** For mutations expected to complete in under 2 seconds, update the UI immediately and reconcile with the server asynchronously.
- **Source:** TanStack Query, Linear changelog; see `response-time-budget.md § Perceived-performance techniques` and `voices/superhuman.md`.
- **Why:** Spinners make the interface feel like a thin shell over a slow server; optimistic updates make the interface feel like the source of truth, which is the entire Linear/Superhuman illusion.
- **Do:** Apply the change locally, show a subtle in-flight indicator if needed, and reconcile/rollback on server response.
- **Don't:** Block the UI with a spinner or disable the affected controls while waiting on the network.
- **Applies to:** Any mutation whose expected round-trip is under ~2s (see principle 4 cross-reference in trigger rules).

### Principle 5 — Motion is semantic, never decorative

- **Statement:** Animate only to communicate a state change or spatial continuity; never to add visual interest.
- **Source:** Rauno, Apple HIG motion; see `motion.md`, `voices/rauno-mechanics.md`, and `foundations.md § Aesthetic-Usability Effect`.
- **Why:** Decorative motion taxes attention on every repetition, and in a CRM where the same transitions fire hundreds of times per session, taxing transitions become actively painful.
- **Do:** Animate enter/exit for new context (dialog, popover, toast); use curves and durations from `motion.md`.
- **Don't:** Animate frequent, user-initiated actions (row toggles, field focus, filter changes).
- **Applies to:** All state transitions; all animation/transition decisions.

### Principle 6 — Affordances appear on hover, not in idle state

- **Statement:** Secondary and tertiary actions on dense surfaces reveal themselves on pointer intent; the idle state stays quiet.
- **Source:** Linear density doctrine; see `voices/linear.md § Actionable principles (principle 1)` (attentional weight).
- **Why:** CRM list rows run 50+ deep; every always-visible chip, button, or icon multiplies by that depth and drowns the actual data.
- **Do:** Reveal row actions on `pointerenter` / focus-within; keep the idle state reserved for primary identity and status.
- **Don't:** Clutter the idle row with hover-only actions baked into every render.
- **Applies to:** List rows, table cells, dense grid surfaces.

### Principle 7 — Inline editing beats modal editing

- **Statement:** Single-field edits happen where the value already lives; modals are reserved for multi-field, multi-step, or destructive flows.
- **Source:** Notion, Linear; see `components/input-inline-edit.md` and `voices/linear.md`.
- **Why:** Modals for one-field edits break flow, steal focus, and force users into an outer shell to change something they can see right now — the opposite of direct manipulation.
- **Do:** Click / Enter to edit, blur / Enter to save, Escape to cancel; persist optimistically.
- **Don't:** Open a dialog to change a single title, status, or owner field.
- **Applies to:** Text fields, single-value edits on rows and detail panels.

### Principle 8 — Undo is free; confirm is expensive

- **Statement:** For reversible destructive actions, act immediately and offer undo; reserve confirm modals for irreversible or high-blast-radius actions.
- **Source:** Shopify Polaris, Peak-End rule; see `foundations.md § Peak-End Rule`, `components/toast-undo.md`, and `_decisions-ledger.md § 2026-04-17 item 9`.
- **Why:** Every confirm dialog is a tax on every successful action to protect against the rare mistake; undo inverts that trade — free on the common path, safe on the rare one.
- **Do:** Execute the destructive action, surface a toast with an undo affordance for N seconds, and fully reverse on click.
- **Don't:** Wrap reversible operations (archive, delete-with-trash, bulk status change) in a confirm modal.
- **Applies to:** Destructive actions that are reversible within the product's own model.

## Conflict-resolution doctrine (always loaded)

- **Motion:** Rauno + Linear > HIG + Material. Don't animate frequent actions. HIG prescribes motion for state continuity; ours restrains based on frequency/novelty. See `motion.md`.
- **Response time:** Stricter target wins in critical flows — Superhuman 50ms in critical paths; Nielsen 100ms is outer bound. See `response-time-budget.md`.
- **Accessibility always > motion.** `prefers-reduced-motion` unconditional.
- **Evidence density:** Foundational laws cite per occurrence; voices cite per quote; component rules attributed at section header.

## Reference index

| Topic | File | When to load |
|---|---|---|
| 10 foundational laws | `references/foundations.md` | When citing authority for a principle |
| Numeric response-time targets | `references/response-time-budget.md` | When generating any async or user-facing interaction |
| Keyboard and focus | `references/keyboard-and-focus.md` | Whenever user interaction is involved |
| Motion rules | `references/motion.md` | Any animation / transition decision |
| Anti-patterns | `references/anti-patterns.md` | Scan output against this before "done" |
| Past decisions | `references/_decisions-ledger.md` | Before re-deciding anything already resolved |
| Per-file templates | `references/_templates.md` | Writing/editing reference files |
| Components (18 — Common 15 + Peek/Drawer/Tabs) | `references/components/*.md` | Generating any Common-15 component or the three v1.1 promotions (peek, drawer, tabs) |
| Cross-cutting patterns | `references/patterns/*.md` | See trigger rules |
| Product voices | `references/voices/*.md` | When channeling a specific voice |
| Tokens (optional) | `references/tokens/*.md` | Visual layer setup (opt-in) |
| Stack binding | `references/stack-bindings/web-primitives.md` | Deciding library/primitive |
| Worked examples | `references/output-format-examples.md` | See shape of output |
| v2 gaps | `references/_v2-gaps.md` | Request is out of v0 scope |

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
