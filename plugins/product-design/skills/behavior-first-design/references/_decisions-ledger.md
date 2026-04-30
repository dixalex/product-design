# Decisions ledger

Resolved design decisions. Consult BEFORE re-deciding. Append new decisions in reverse-chronological order with date + call reference + rule.

## 2026-04-21

- **Spot audit 2 (fresh-Claude simulation) findings — deferred, not fixed.** Reviewed `components/lead-row.tsx`, `components/command-palette.tsx`, `components/lead-detail-peek.tsx` against the 8 principles. Zero strict violations. Two minor observations deferred: (a) `command-palette.tsx` line 88 has redundant `zIndex` (inline style `zIndex: 90` + className `z-[var(--z-dialog)]`) — cosmetic; resolve when consolidating z-index tokens. (b) `lead-detail-peek.tsx` line 44 treats Space as a close key — acceptable for v0 (panel has no scrollable interactive content), but will conflict with default Space = page-scroll if peek gains scrollable children in v1.1. Flag for peek-component generalization (see NEXT.md v1.1 Peek). Rationale for deferring: neither observation is a principle violation; both are forward-looking code-quality notes.
- **Skill hardening from 2026-04-21 mini-lead-crm manual walkthrough.** Added: (A) list-row auto-focus-on-mount invariant; (B) unified-cursor rule — mouse `mousedown` moves keyboard focus index; (C) non-modal overlay Esc attaches to window with dialog-open guard (new scenario in `keyboard-and-focus.md`); (D) Tailwind v4 requires `@tailwindcss/postcss` + `postcss.config.mjs` (added to `stack-bindings/web-primitives.md § Installation`). Each fix traces to a specific manual-test bug in `examples/mini-lead-crm/`.
- **Promoted Peek, Drawer, Tabs to v0 skill (were v1.1 deferred).** Peek was a gap surfaced by `examples/mini-lead-crm/components/lead-detail-peek.tsx` — the scaffold composed a peek without the skill defining the primitive, which is exactly the drift mode the reference library exists to prevent. Drawer and Tabs are cheap Base-UI-primitive wrappers (Base UI ships both with full WAI-ARIA + keyboard + focus) and deferring them cost more in scaffold ambiguity than their 600–900 words cost to write. New files: `components/peek.md`, `components/drawer.md`, `components/tabs.md`. Wiring: `SKILL.md` reference-index count bumped from 15 to 18 with "Common 15 + three v1.1 promotions" framing; `stack-bindings/web-primitives.md § Per-component binding` gains three rows; `_v2-gaps.md § Component coverage beyond Common 15` strikes Drawer/Peek/Tabs/ContextMenu (ContextMenu remains DropdownMenu-covered in v0). Musk 10% re-add measurement: 3 of 7 deferred components promoted — at the "miscalibrated" threshold; see `_v2-gaps.md § v0 scope-correctness signal` for the lesson.
- **Rebound ⌘N → ⌘⇧N in mini-lead-crm scaffold** (`examples/mini-lead-crm/components/new-lead-dialog.tsx` + `tests/keyboard-nav.spec.ts` + `tests/focus-landing.spec.ts` + `tests/perf-budget.spec.ts`). Plain ⌘N collides with Chrome/Safari/Firefox "New Window" on macOS (and Ctrl+N on Windows/Linux). ⌘⇧N matches Linear/Raycast/Superhuman convention. Detection uses `(e.metaKey || e.ctrlKey) && e.shiftKey` so Linux/Windows users get Ctrl+Shift+N. No corresponding skill-reference change — the shortcut rebinding is scaffold-specific, not a library-wide rule.

## 2026-04-18

- **Base UI package renamed: `@base-ui-components/react` → `@base-ui/react`.** Stable at 1.4.0 (shipped 2025-12-11). Task 1 spike confirmed current version. Use `@base-ui/react` everywhere.
- **Pre-1.0 Base UI risk resolved.** Spec § 9 risk 1 mitigation complete — Base UI is post-1.0 stable.

## 2026-04-17

1. **Stack: Base UI + react-aria/cmdk/hand-rolled fallbacks.** Reason: Operate uses Base UI. 6/15 Common-15 components have no Base UI primitive (DatePicker, CommandPalette, ListRow, Filter, StatusPill, EmptyState). Use named fallbacks per `stack-bindings/web-primitives.md`.
2. **Form validation timing: on-blur default.** On-submit for destructive. On-change for password/uniqueness. See `patterns/form-validation-timing.md`.
3. **Error surface: inline for field errors, toast for transient system, dialog only for blocking destructive.** See `patterns/error-surface.md`.
4. **Selection: single-click opens (Space toggles peek); cmd/ctrl-click toggles select; shift-click selects range.** Linear/Gmail parity. See `patterns/multi-select.md`.
5. **Keyboard: j/k AND ↑/↓ both active by default.** Vim parity and standard parity simultaneously. See `components/list-row.md`.
6. **Motion: Rauno > HIG on frequency-based restraint.** Don't animate repetitive actions. See `motion.md` + `voices/rauno-mechanics.md`.
7. **Response time: 100ms outer bound (Nielsen 1993); 50ms ambition in critical paths (Superhuman).** See `response-time-budget.md`.
8. **Accessibility always beats motion.** `prefers-reduced-motion` respected unconditionally.
9. **Undo > confirm for reversible actions.** Confirm modal only for destructive-irreversible. See principle 8 in SKILL.md.
10. **Skill v0 is reference-on-build only.** Audit/review (Pattern B) deferred to v2. Per 2026-04-17 call.
11. **Working memory = Cowan ~4 chunks, not Miller 7±2.** Updated `foundations.md § Miller's Law` to cite Cowan 2001 as primary, Miller 1956 as historical. Applies to palette command count, list row action count, form field grouping.
12. **Skill framing: pattern library + mental-model scaffolding.** Claude applies via Recognition-Primed Decision (Klein 1998). The Common 15 IS the prototype library. See SKILL.md § "What this skill is, cognitively".
13. **Anti-patterns = deliberate inversion** (Jacobi / Munger). We enumerate failure paths; other references answer the positive "what works" question.

## 2026-04-06
- Pine Event CRM build: originally "reverse-engineer Close.io" per call notes.

## 2026-04-07
- Build direction: minimal internal CRM replacing Close.io. Reference architecture = Operate. Stack = Next.js + Tailwind + Base UI.

<!-- Append new decisions above in reverse chronological order. -->

## 2026-04-29 (v0.4.0 reshape)

1. **Skill is a process skill, not a reference library.** Mimics `superpowers:brainstorming` and sibling product-architecture/visual-design. Gates between layers; terminates in a behavior spec. Source: spec `2026-04-28-product-design-plugin-v0.4.0-design.md § 1.2`.
2. **6 layers: Inputs → Focus & Keyboard → Response-time & Optimism → Feedback & Recovery → Motion fire-policy → Component binding.** Source: spec § 2.1.
3. **Pipeline position: between visual-design and superpowers:writing-plans.** Source: spec § 3.1.
4. **Derivation method per layer.** Inputs: modality priority matrix. Focus: chord-map + state machine. Response-time: Nielsen budgets. Feedback: status decision tree. Motion: frequency-novelty. Binding: surface-action × component matrix. Source: spec § 2.1.
5. **Spec format: prose decisions + YAML frontmatter; NO CSS tokens** (visual-design owns tokens; behavior-first-design references them by name). Source: spec § 2.2.
6. **6-step output contract dies.** Replaced by spec emission. Source: spec § 1.2.
7. **Common 18 components survive as Layer 6 input** (consumed, not emitted as code). Source: spec § 2.4.
8. **Mid-session checkpoint after Layer 3** — opt-in pause to avoid 6-layer fatigue (mirror of visual-design's Color-layer checkpoint). Source: spec § 2.7.
9. **User-supplied voices protocol inherited** from visual-design (one source of truth). Source: spec § 2.7.
10. **HARD-GATE wording** (new, mirrors visual-design): *"Do NOT invoke superpowers:writing-plans... until the behavior spec is written to disk AND the user has explicitly approved it."* Source: spec § 2.7.

## 2026-04-30 (v0.4.1)

1. **Added Mermaid output guidance** to Focus & Keyboard + Component binding layer files; template updated. Focus state machines as `stateDiagram-v2` per focus-trap surface; binding maps as `graph LR` (complements the table; table primary). Source: spec `2026-04-30-product-design-plugin-v0.4.1-design.md § 1.2`.
