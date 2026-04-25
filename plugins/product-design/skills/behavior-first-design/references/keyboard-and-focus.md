# Keyboard & focus management

## Principles
1. Every interactive element keyboard-activatable.
2. Focus is a cursor you manage deliberately.
3. `:focus-visible` only for keyboard; never `:focus` with pointer (Primer).

These three rules compress roughly a decade of accessibility literature. Principle 1 is the floor (WCAG 2.1.1). Principle 2 reframes focus from "browser default" to "product concern" — every state change that rearranges the DOM forces a design decision about where the cursor now lives. Principle 3 rejects the common anti-pattern of stripping `outline: none` to hide the blue ring on mouse click: use `:focus-visible` so pointer users never see the ring but keyboard users always do (see `foundations.md § Norman's Affordances + Signifiers` — `:focus-visible` is the signifier for keyboard state: keyboard users need the ring, pointer users don't). Primer: "never remove the focus indicator unless you are replacing it with a `border` or some other visual indicator."

## Focus-management scenarios

### On content added

Move focus to the first newly-added item unless a more logical spot exists. Primer's canonical example: a "Load more" button that reveals additional comments — focus moves to the first new comment, not back to the button that was just pressed. Rationale: keyboard users who triggered the load expected navigation to progress; leaving focus on the trigger forces them to Tab through every revealed item to reach the content they asked for.

Per `_decisions-ledger.md` § 2026-04-17 item 4: first added item is the default. Exception cases — the "more logical spot" clause — include wizard-style flows where the next step's primary action is more useful than the new content itself, and streaming responses where focus should follow the writing cursor rather than the first chunk.

### On content removed

Return focus to a logical spot; never leave it dangling at `<body>`. Primer: "By default, if you remove an item and don't tell the focus where to go next, it will return to the `body` element and the user will need to navigate through the entire page again to get to where they were."

Suggested order of preference (Primer): adjacent sibling in the same list (next comment → previous comment), a related input if one exists (e.g. "write comment" textarea after a thread action), then a container heading. The decision is contextual — "use best judgement when deciding what will be logical for a user" — but the failure mode is identical across cases: losing focus to `<body>` destroys assistive-tech users' sense of place.

### On dialog open / close

Per Atlassian's Modal Dialog spec:

- **On open**: focus moves to the first available interactive element. Atlassian's strong default is the close button in the header (via `hasCloseButton` on `ModalHeader`). If the close button can't be first in the DOM naturally, render it first and reorder visually with flex `row-reverse` — Atlassian's example does exactly this, with the comment: "This ensures users of assistive technology get all relevant content."
- **When there's no close button**: focus goes to the modal's title. The title should be wrapped with `tabIndex={-1}` and receive a ref so it can accept programmatic focus without appearing in the Tab sequence.
- **When there's no title**: focus goes to the dialog container itself.
- **On close**: focus returns to the trigger.
- **When the trigger is deleted** (e.g. confirm-delete-row modals): "focus should go to the next focusable element on the page." Atlassian: "If you remove an element from the DOM and don't set the focus, it returns to the body element at the top of the page. And for people using assistive technology, this means they'll need to navigate through the entire page to return to where they originally were, which we don't want."

Additionally: focus is trapped inside the dialog while open (a common mistake is to let Tab escape to the page behind), and Esc closes the dialog (Nordhealth: "Dialogs and overlays can be closed using esc key"). The close interaction must hit its budget — see `response-time-budget.md § Numeric targets` for the 100ms dialog-open target.

### On non-modal overlay dismissal

Non-modal overlays (peek panels, transient drawers without focus trap, inline disclosure) attach Esc at `window` scope with a guard: bail if `[role="dialog"][data-state="open"], [role="alertdialog"][data-state="open"]` is present (higher-priority Base UI modal handles Esc first). Reserve focus-inside-overlay Esc handling for when the user has Tab'd into the overlay content — keep both handlers, the window listener is the common path.

This distinguishes non-modal from modal (see `### On dialog open / close` — modals trap focus, so focus-inside-overlay Esc works automatically). Non-modal overlays don't trap, so the user's focus typically stays on the originating surface (e.g. a list), and Esc must fire from there.

### On filter / search

Primer's rule: focus stays in the search or filter input while results update. Results are announced via an ARIA live region; Esc clears the input and exits.

Why not move focus to results? Because "if you have five filters and apply the first filter, you do not want to be moved to the results because you may not be ready to move on." The user's intent is still "I am composing a query"; premature focus movement violates that intent.

Focus management only becomes necessary when a filter element itself is removed — e.g. pressing a "Clear filters" pill that then disappears. At that point, fall back to the On content removed rules. And if the filter is implemented as a link causing a full page reload, focus management is "a nice to have rather than a requirement" — skip links and heading structure matter more.

### When NOT to move focus (Polaris doctrine — critical)

Shopify's Polaris adds a constraint the other three sources only imply: don't programmatically move focus without merchant/user input. The merchant's cursor belongs to them. Taking it without consent is jarring, breaks flow, and on assistive tech it steals screen-reader context mid-sentence.

- **DO move focus**: link-navigation (user activated a link that goes elsewhere in the page); overlay-needs-access (merchant must access an overlay to proceed); form-error-on-submit (merchant submitted, result was an error — move to the error message).
- **DON'T move focus**: content updates in the background (new chat message, new notification, live-updating dashboard); user actively working elsewhere on the page.

The exception Polaris carves: "when the merchant needs to be interrupted because they cannot continue their current workflow." A hard block — not a soft nudge. If the interruption isn't load-bearing, announce it in a live region and leave focus alone.

## Keyboard contracts per archetype

The following tables describe the canonical keyboard interactions for three common archetypes. Menu/Dropdown and Dialog tables are reconstructed from Radix Primitives conventions (https://www.radix-ui.com/primitives/docs/components/dropdown-menu#keyboard-interactions, https://www.radix-ui.com/primitives/docs/components/dialog#keyboard-interactions) — source HTML was not scraped into `.firecrawl/scratch/`, so a future editor should verify against the live Radix docs. The Listbox/List row contract reflects Pine's house decision in `_decisions-ledger.md` § 2026-04-17 item 5.

### Menu / Dropdown

Reconstructed from Radix Primitives keyboard conventions. Verify against live docs before publishing downstream.

| Key | Action |
| --- | --- |
| `Space` / `Enter` (on trigger) | Open menu, focus first item |
| `ArrowDown` (on trigger) | Open menu, focus first item |
| `ArrowUp` (on trigger) | Open menu, focus last item |
| `ArrowDown` / `ArrowUp` (within menu) | Move focus to next / previous item, wrapping |
| `Home` / `End` | Focus first / last item |
| `Enter` / `Space` (on item) | Activate item, close menu |
| `ArrowRight` (on submenu trigger) | Open submenu, focus first item |
| `ArrowLeft` | Close submenu, return focus to parent trigger |
| `Esc` | Close menu, return focus to trigger |
| `Tab` | Close menu, advance focus normally |
| Typeahead (printable chars) | Focus first item whose label starts with typed string |

### Dialog

Reconstructed from Radix Primitives Dialog keyboard conventions.

| Key | Action |
| --- | --- |
| `Space` / `Enter` (on trigger) | Open dialog |
| `Tab` | Move focus to next focusable element inside the dialog; wraps at end |
| `Shift` + `Tab` | Move focus to previous focusable; wraps at beginning |
| `Esc` | Close dialog, return focus to trigger (or next-focusable if trigger deleted — see On dialog open / close) |

Focus is trapped: Tab past the last element cycles to the first, never to the page behind. Body scroll is locked for the duration.

### Listbox / List row

Pine's default — per `_decisions-ledger.md` § 2026-04-17 item 5: "Keyboard: j/k AND ↑/↓ both active by default." Vim parity and standard parity simultaneously. No toggle, no setting — both mappings are always live.

j/k only bind when focus is inside a listbox composite (a `role="listbox"` or `role="grid"` subtree) — never on inputs, textareas, or contenteditable elements, where those characters must type literally.

| Key | Action |
| --- | --- |
| `ArrowDown` / `j` | Focus next row |
| `ArrowUp` / `k` | Focus previous row |
| `Home` | Focus first row |
| `End` | Focus last row |
| `Space` | Toggle peek/preview of focused row |
| `Enter` | Open focused row |
| `Tab` | Leave list, advance to next focusable element outside |
| `Shift` + `Tab` | Leave list, move to previous focusable element outside |
| Typeahead | Focus first row matching typed prefix (optional, enable per list) |

Notes: only one Tab stop per list — the list itself — with `ArrowDown`/`j` handling intra-list navigation. This is the WAI-ARIA composite-widget pattern and matches Radix's approach for `Listbox`/`RovingFocusGroup`. Rationale for dual j/k + arrow support: Pine's research CRM users include both power users (vim muscle memory) and general users (standard arrows); picking one alienates the other, and wiring both costs nothing.

## Anti-patterns (from Nordhealth)

- No `autofocus` attribute — use imperative `focus()` when needed. Because `autofocus` steals focus on page load and fires unpredictably on SPA route changes, violating the Polaris "don't move focus without user input" doctrine above.
- No `tabindex` values other than 0 or -1. Because positive `tabindex` breaks DOM source order, which is the accessibility contract screen readers rely on.
- No `title` attribute tooltips — use real Tooltip component. Because `title` tooltips have no keyboard access, no mobile access, and no ARIA surface — they appear only for mouse hover.
- No `aria-label` if visually-hidden text works — VH is more reliable for screen readers. Because visually-hidden text renders in the accessibility tree natively; `aria-label` can be stripped or ignored by some assistive-tech configurations, and it's invisible to sighted users who might otherwise help debug.
- Every interactive element has visible focus styles. Because without a visible ring, keyboard users are navigating blind — the `:focus-visible` style IS the interaction affordance.

## Sources

- Primer (GitHub) accessibility design guidance — Focus management. https://primer.style/accessibility/design-guidance/focus-management/
- Atlassian Design System — Modal Dialog focus management. https://atlassian.design/components/modal-dialog/examples#focus-management
- Polaris (Shopify) — Accessibility, Managing focus to support merchant workflows. https://polaris-react.shopify.com/foundations/accessibility
- Nordhealth Design — Accessibility checklist (Keyboard, Markup sections). https://nordhealth.design/accessibility-checklist/
