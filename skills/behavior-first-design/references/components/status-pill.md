# Status Pill

A compact label that communicates the state of an entity (lead status, deal stage, task state, sync status) in-line, typically inside a list row, detail header, or field value. Status is the **signifier of state** per `foundations.md § Norman's Affordances + Signifiers` — the pill is how the user knows what phase something is in.

**Base UI has no StatusPill primitive** (and no `<Badge>` primitive either). Per `_decisions-ledger.md § 2026-04-17` (item 1), StatusPill is one of the six gaps — but it's trivial: a `<span>` with CSS. No library dependency required. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Checklist (filled before generating code)

```yaml
disabled_state: n-a
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: n-a
keyboard_activation: n-a
esc_closes: n-a
validation_timing: n-a
color_plus_text_plus_shape_or_icon: yes
color_alone_forbidden: yes
text_contrast_ratio_min: 4.5
non_text_contrast_ratio_min: 3.0
consistent_shape_per_status_family: yes
min_height_px: 16
min_tap_target_if_clickable_px: 40
live_indicator_carries_aria_live: conditional
uppercase_text: no
icon_before_label_optional: yes
```

## Keyboard contract

Status pills are **not interactive by default** — they're read-only state labels. If clickable (e.g., a pill that filters the list by that status on click), they follow button semantics per `components/button.md`.

| Key | When | Effect |
| --- | --- | --- |
| `Tab` | Non-interactive pill | Skipped (not in tab order) |
| `Tab` | Interactive pill | Focus on pill; visible ring |
| `Enter` / `Space` | Interactive pill focused | Activate (e.g., apply as filter) — see `components/button.md` |

If a pill carries a **live-updating** status (e.g., "Saving…", "Synced 2s ago", "Connection lost"), the container needs `aria-live="polite"` so screen readers announce transitions. Cross-ref `patterns/real-time-indicators.md`.

## Behavior invariants

- **Color + text + (icon OR shape) — never color alone.** Per WCAG 1.4.1 Use of Color. A red dot alone fails; a red dot + "Lost" + a rounded-rectangle pill passes. This is the single hardest rule to enforce at review time because designers love color-only status maps.
- **Text contrast ≥ 4.5:1** (WCAG 2.2 AA, normal text). Non-text contrast (pill border, icon) ≥ 3:1. Test every color pair — most pastel "status" palettes fail 4.5:1 on light backgrounds.
- **Consistent shape per status family.** Lead statuses all use a rounded rectangle; sync indicators all use a dot; stage pills all use a colored-border pill. Mixing shapes within a family makes the shape meaningless as a differentiator.
- **Minimum height 16px.** Below that, text is unreadable and the pill loses shape. If the pill is interactive, the **tap target** is ≥40×40 CSS px (Fitts / WCAG 2.5.5) — add padding around the visible pill if needed; the visible pill can stay 16–20px.
- **Uppercase is optional, sparingly.** Uppercase status text ("NEW") reads as shouting and fails some dyslexia accommodations. Prefer title-case ("New", "Qualified") unless your brand voice explicitly calls for uppercase.
- **Status labels are nouns or short adjectives.** "New", "Qualified", "Contacted", "Won", "Lost" — not "Has been qualified today" or "Pending further action". Keep to 1–2 words.
- **State families must be visually enumerable.** A user should see the pill and immediately know which family it belongs to (lead status vs task state vs sync indicator). Use distinct shapes OR distinct color spaces per family.
- **Live sync/save indicators** carry `aria-live="polite"` so transitions ("Saving…" → "Saved") are announced without stealing focus. Cross-ref `patterns/real-time-indicators.md`.

## State transitions

Status pills are stateless at the component level — the parent owns the status value and re-renders the pill with a new `value` prop. If the pill animates between states (e.g., "Saving…" → "Saved"), the motion rule per `motion.md § The frequency-and-novelty matrix` applies: subtle cross-fade ≤150ms, no bounce, respect `prefers-reduced-motion` (instant swap).

```
rendered-state (value="New", color=blue, icon=circle)
  ↓ parent sets value="Qualified"
rendered-state (value="Qualified", color=green, icon=check)
  — if animated: 150ms cross-fade; instant if prefers-reduced-motion
```

Canonical status families for Pine Event CRM (this file documents the primitive; specific status sets are a policy decision per-entity, not hardcoded here):

- **Lead status:** New · Contacted · Qualified · Unqualified · Won · Lost
- **Task state:** Open · In progress · Done · Cancelled
- **Sync indicator:** Saving · Saved · Error · Offline

## Stack binding (Base UI)

**Base UI has no StatusPill or Badge primitive.** Hand-roll: a `<span>` with Tailwind utility classes (`inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-xs font-medium`) plus a color pair (`bg-green-100 text-green-800`) and optional leading icon (Lucide). If the pill is interactive, wrap in a `<button>` per `components/button.md`. For animated transitions (save/sync), wrap in the project's live-region component and set `aria-live="polite"`. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- **Color-only status** — "red = bad, green = good" with no text or icon. Fails WCAG 1.4.1; fails colorblind users (`anti-patterns.md § Accessibility anti-patterns`).
- **Pastel backgrounds with light text** — contrast fails 4.5:1 silently; no visual regression catches it.
- **Mixing shapes within a family** — one lead status is a dot, the next is a pill; shape stops communicating family.
- **10+ status values** — user can't hold them in memory (`foundations.md § Miller's Law`, `§ Hick's Law`). Collapse to ≤6 canonical states; edge cases go in a tooltip.
- **Interactive pill with no focus ring** — keyboard users can't see where they are.
- **Live "Saving…" indicator without `aria-live`** — screen readers never hear that save happened (`patterns/real-time-indicators.md`).
- **Pill text that doesn't match the sort/filter label** — UI says "Qualified", filter says "Lead qualified"; user doesn't connect them (`voices/linear.md § Actionable principles`).
- **Animated pill on every list row when the page loads** — dozens of pills bouncing in = visual noise (`motion.md § The frequency-and-novelty matrix`).

## Principles invoked

- Principle 2 (obvious affordances) — status IS the signifier of state; color + text + shape make it unambiguous.
- Principle 3 (keyboard parity) — non-interactive pills stay out of tab order; interactive ones get a visible focus ring.
- Principle 8 (accessibility always beats aesthetic) — color alone is forbidden even if it looks cleaner.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- WCAG 2.2, 1.4.1 Use of Color — https://www.w3.org/WAI/WCAG22/Understanding/use-of-color.html
- WCAG 2.2, 1.4.3 Contrast (Minimum) — https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html
- WCAG 2.2, 1.4.11 Non-text Contrast — https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html
- WCAG 2.2, 2.5.5 Target Size — https://www.w3.org/WAI/WCAG22/Understanding/target-size-enhanced.html
- Linear stage/status conventions — https://linear.app/method/status
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI StatusPill gap)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Norman's Affordances + Signifiers`, `§ Fitts's Law`, `§ Miller's Law`, `§ Hick's Law`
- `patterns/real-time-indicators.md`
- `motion.md § The frequency-and-novelty matrix`, `§ prefers-reduced-motion`
- `anti-patterns.md § Accessibility anti-patterns`
- `voices/linear.md § Actionable principles`
- `components/button.md` (interactive variant)
