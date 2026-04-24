# DatePicker

A combined text-input-plus-calendar-popover for selecting a single date (or date range). The user may type into the field, open a calendar grid, or both. Grid-widget semantics: the calendar is a 2D `role="grid"` with arrow-key navigation, not a listbox.

**Base UI has no DatePicker primitive.** Per `_decisions-ledger.md § 2026-04-17` (item 1), DatePicker is one of the six Base UI gaps. Use react-aria's `useDatePicker` hooks as the **primary stack**; fall back to `react-day-picker` if the project already depends on it. See `stack-bindings/web-primitives.md` for the decision and wiring (Task 17 — forward reference).

## Checklist (filled before generating code)

```yaml
disabled_state: yes
loading_state: n-a
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
keyboard_activation: both
esc_closes: yes
validation_timing: on-blur
typing_input_allowed: yes
format_locale: user-preference
min_max_bounds: configurable
disabled_dates_supported: yes
calendar_role: grid
day_cell_role: gridcell
day_cell_min_size_px: 32
selected_has_text_or_icon: yes
today_indicator: yes
week_start_day: locale-default
popover_open_paint_ms: 100
enter_motion_ms: 150
```

## Keyboard contract

Reproduced from react-aria `useDatePicker` / `useCalendar` / `useCalendarGrid` (https://react-spectrum.adobe.com/react-aria/useDatePicker.html, https://react-spectrum.adobe.com/react-aria/useCalendar.html) and the WAI-ARIA Date Picker Dialog pattern (https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/examples/datepicker-dialog/). Two layers: **text field** and **calendar grid**.

| Key | When | Effect |
| --- | --- | --- |
| Typing | Field focused | Parse per locale; commit on blur |
| `ArrowDown` | Field focused | Open popover; focus today or selected |
| `Enter` | Field focused | Commit typed value |
| `Esc` | Field focused | Revert to last committed |
| `ArrowLeft` / `ArrowRight` | Day focused | Previous / next day (wraps) |
| `ArrowUp` / `ArrowDown` | Day focused | Same weekday in previous / next week |
| `Home` / `End` | Day focused | First / last day of current week |
| `PageUp` / `PageDown` | Day focused | Same day in previous / next month |
| `Shift` + `PageUp` / `PageDown` | Day focused | Same day in previous / next year |
| `Enter` / `Space` | Day focused | Select; close; focus → field |
| `Esc` | Popover open | Close without change; focus → field |
| `Tab` | Popover open | Cycle through popover controls |

Disabled dates are **skipped by arrow nav** but stay in the DOM with `aria-disabled="true"`.

## Behavior invariants

- **Grid widget, not listbox** — `role="grid"` with `gridcell` days and `columnheader` weekday labels. Home/End act on the current *week*; PageUp/Down on month; Shift+Page on year.
- **Typing is first-class** — users typing `12/03/2026` must not be forced into the calendar. Parse per locale on blur (`patterns/form-validation-timing.md § Decision table`); invalid input surfaces inline (`patterns/error-surface.md`).
- **Locale-aware format and parse** — `en-US` → `MM/DD/YYYY`, `en-GB` → `DD/MM/YYYY`, ISO `YYYY-MM-DD` always accepted. react-aria's `useDatePicker` + `@internationalized/date` handles this natively.
- **Min/max bounds and disabled dates** — `minValue`, `maxValue`, and `isDateUnavailable(date)` for per-date rules (weekends, holidays, booked ranges). Disabled days carry `aria-disabled="true"` and are skipped by arrow nav.
- **Day cell ≥32×32 CSS pixels** (44×44 for touch) per `foundations.md § Fitts's Law`.
- **Default to month view**, not a wall of 365 cells (`foundations.md § Hick's Law`). Year/decade zoom is progressive disclosure via header click.
- **Today indicator is non-color** (ring, dot); selected day carries `aria-selected="true"` + a non-color cue (border, glyph, inverted fill). Focused (arrow cursor) and selected (committed) are distinct visual states.
- **Popover opens ≤100ms, enter 150ms** (`response-time-budget.md § Numeric targets`, `motion.md § Duration defaults`); reduced-motion collapses to an instant swap.

## State transitions

```
closed (field shows committed value or placeholder)
  ↓ click calendar icon | ArrowDown in field
opening (150ms enter; popover positioned via Floating UI)
  ↓ animation ends
open (grid mounted; focus → today OR currently-selected date)
  ↓ Arrow/Home/End/Page → focused-day changes (skip disabled)
  ↓ click month/year header → month-view → year-view (progressive disclosure)
  ↓ Enter | Space | click day → selected → closing → closed (focus → field)
  ↓ Esc | click-outside → closing → closed (no change; focus → field)

typing (field focused)
  ↓ characters entered
parsing (per locale + ISO fallback)
  ↓ parse succeeds on blur | submit → committed
  ↓ parse fails on blur → error-state (inline via patterns/error-surface.md)
  ↓ Esc → revert to last committed

range-select variant
  ↓ first-click → anchor; second-click → range committed
  ↓ Esc between clicks → cancel; anchor cleared
```

Focus follows `keyboard-and-focus.md § On dialog open / close` (return to field on close; next-focusable if field deleted).

## Stack binding (Base UI)

**Base UI has no DatePicker primitive.** Use react-aria's `useDatePicker`, `useCalendar`, `useCalendarGrid`, `useCalendarCell`, and `useDateField` hooks as the **primary stack** — they own grid semantics, keyboard map, locale parsing/formatting (`@internationalized/date`), and min/max/unavailable-date logic. Pair with React Aria Components (`<DatePicker>`, `<Calendar>`, `<DateField>`) for styled primitives or bare hooks for full visual control. **Fallback:** if the project already depends on `react-day-picker` (gpbl), use it to avoid dual-dependency bloat. Do NOT invent a DatePicker from scratch. See `stack-bindings/web-primitives.md` (Task 17 — forward reference).

## Common mistakes

- Forcing calendar-only entry (no typing) — violates `foundations.md § Tesler's Law`.
- Listbox semantics for the calendar — it's a 2D grid; listbox breaks AT navigation.
- Opening on a year-view wall of 365 cells — violates `foundations.md § Hick's Law`. Default to month.
- Hardcoded US format in a global product — silently corrupts `en-GB` data.
- Missing `aria-selected` / `aria-disabled` (`anti-patterns.md § Accessibility anti-patterns`).
- Day cells <32×32 px desktop, <44×44 touch — fails Fitts.
- Validating mid-typing — validate on blur or on submit (`patterns/form-validation-timing.md § Rationale`).
- Errors surfaced in a modal or tooltip instead of inline (`patterns/error-surface.md`).
- Spring/bounce on selection (`anti-patterns.md § Behavior anti-patterns`; `motion.md § Easing`).
- No today indicator — users lose spatial anchor when arrowing across months.

## Principles invoked

- Principle 1 (speed of interaction) — popover open <100ms; typing commits on blur without round-trip.
- Principle 2 (obvious affordances) — today indicator, `aria-selected` + non-color cue, `aria-disabled` for out-of-bound, 32×32 minimum.
- Principle 3 (keyboard parity) — arrows (days), Home/End (week), PageUp/Down (month), Shift+Page (year), Enter, Esc.
- Principle 4 (information scent / progressive disclosure) — month view default; year/decade zoom opt-in.
- Principle 6 (reversibility / safe defaults) — Esc reverts typed value; selection replaceable before submit.
- Principle 7 (respect the user's cursor) — return focus to the field on every close path; no autofocus into the popover.

(Principle numbers finalize in SKILL.md — Task 20.)

## Sources

- react-aria, `useDatePicker` — https://react-spectrum.adobe.com/react-aria/useDatePicker.html
- react-aria, `useCalendar` — https://react-spectrum.adobe.com/react-aria/useCalendar.html
- react-aria, `useDateField` — https://react-spectrum.adobe.com/react-aria/useDateField.html
- WAI-ARIA Date Picker Dialog pattern — https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/examples/datepicker-dialog/
- react-day-picker (gpbl) — https://react-day-picker.js.org/
- `@internationalized/date` (Adobe) — https://react-spectrum.adobe.com/internationalized/date/
- `_decisions-ledger.md § 2026-04-17` item 1 (Base UI DatePicker gap)
- `stack-bindings/web-primitives.md` (Task 17 — forward reference)
- `foundations.md § Fitts's Law`, `§ Hick's Law`, `§ Tesler's Law`, `§ Jakob's Law`
- `patterns/form-validation-timing.md § Decision table`, `patterns/error-surface.md`
- `keyboard-and-focus.md § On dialog open / close`, `§ Keyboard contracts per archetype`
- `response-time-budget.md § Numeric targets`
- `motion.md § Duration defaults`, `§ prefers-reduced-motion`
- `anti-patterns.md § Behavior anti-patterns`, `§ Accessibility anti-patterns`
- `components/input-inline-edit.md`, `components/combobox-select.md`, `components/popover.md`
