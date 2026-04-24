# Voice: Eagle (Pine Research in-house principles)

## Source

- `pine-research/design/eagle-product-principles.md` — full 24-section principle set.
- This voice extracts **behavioral** sections: §6 Interaction, §7 Progressive Disclosure, §9 Feedback, §13 Performance, §17 Batch, §21 Onboarding, §24 Error Handling. For non-behavioral sections (data, visual, sync, plugins) consult the full file.

## Core quotes (verbatim, attributed)

> "Every item should be explorable at three levels of commitment: Zero-click… One-action… Full engagement." — Eagle §6.1

> "Every modal interruption breaks flow." — Eagle §6.9

> "Show the minimum viable information for each item by default… Never front-load all metadata — it overwhelms." — Eagle §7.1

> "The inspector eliminates the 'peek and return' loop." — Eagle §7.4

> "Any operation that takes more than ~200ms should show progress feedback… Never leave the user staring at an unresponsive UI." — Eagle §9.4

> "Default to the higher-quality experience. Provide a performance mode for users on slower hardware. Label the trade-off honestly." — Eagle §13.1

> "Lists with more than ~100 items should use virtual scrolling… a non-negotiable performance requirement." — Eagle §13.3

> "Batch operations should build on standard multi-select (Shift-range, Cmd-individual, Cmd-A all)." — Eagle §17.1

> "An empty list with 'Drag files here or press Cmd+V to paste' teaches the user a capability while acknowledging the emptiness." — Eagle §21.1

> "The first-run experience should get one item into the system within 30 seconds." — Eagle §21.3

> "When an external service is unavailable, the product should continue functioning with reduced capability rather than breaking entirely." — Eagle §24.1

> "Every action should be undoable… Undo reduces the cost of experimentation." — Eagle §24.6

## Actionable principles

1. **Three-level commitment before full navigation.** Every CRM item (Lead, contact, activity, filter) is explorable at three depths: hover-preview, one-action peek, full nav. Never force a context switch just to check whether this is the right item. (§6.1, §7.4)

2. **Inline edit first, modal last.** Double-click to rename, click-to-edit inspector fields. Reserve modals for multi-step flows needing isolation. A modal for a single-field change is a failure. (§6.9)

3. **Multi-select is the foundation; batch actions compose on top.** Shift-range, Cmd-individual, Cmd-A all — everywhere a list appears. Selection reveals batch actions in the toolbar. Never ship a "batch mode" toggle. (§6.8, §17.1)

4. **200ms is the feedback threshold.** Below: instant inline feedback. Above (import, export, bulk tag, library merge): progress indicator, counter, or cancellable state. Silent long-running ops are broken. (§9.4)

5. **Four independent feedback channels: sound, toast, inline, modal.** Disabling toasts keeps inline highlights; disabling sound keeps modals on destructive actions. Never wire all feedback to a single on/off. (§9.1, §9.3)

6. **Virtual scroll any list over 100 items. Quality by default, speed as opt-in.** Default to the richer mode; expose a labeled escape hatch for slower machines. Hide the cost, not the toggle. (§13.1, §13.3)

7. **Empty states teach; first-run delivers value in under 30 seconds.** Never a blank panel. Every empty list carries the canonical CTA (create Lead, import CSV, paste URL). First-run puts one Lead into the system before any account / workspace setup. (§21.1, §21.3)

8. **Graceful degradation and explicit undo everywhere.** External service down → local capability still works. Every user action has an undo within a visible window. Silent failures and irreversible actions are forbidden. (§24.1, §24.3, §24.6)

## When to load this voice

Load `voices/eagle.md` when any of these apply:

- User says **"per Eagle"**, **"like our other product"**, or references an internal precedent.
- **Batch operation** design (bulk tag, move, export, assign) — §17.
- **Empty state** generation (empty List, Inbox, Saved view, filter result) — §21.
- **Error handling / failure-mode** — sync failure, external-service down, duplicate detection, path-moved recovery — §24.
- **Feedback / notification** patterns (toast? sound? inline? modal?) — §9.
- **Progressive disclosure** — density, inspector-vs-modal, three-level commitment — §6, §7.
- **Performance tradeoff** writing — quality-default vs. speed-escape-hatch, virtual-scroll threshold — §13.
- A sibling Eagle pattern has already solved a near-identical problem — defer to Eagle before inventing.

**For non-behavioral concerns** (data model, visual system, sync, plugins), skip this voice and load `pine-research/design/eagle-product-principles.md` directly.

See also: `foundations.md § Doherty Threshold`, `response-time-budget.md § Numeric targets`, `anti-patterns.md`, `_decisions-ledger.md`.
