# Error surface

Match the error's surface to the user's position in the flow. Field problems stay near the field. Transient system problems rise to a toast the user can ignore. Blocking destructive failures seize the foreground via a dialog. In-progress context stays inline with the thing being operated on. Never promote a small error to a big surface — it breaks flow without adding signal.

## Decision table

| Error type | Surface | Example |
|---|---|---|
| Field validation | Inline next to field | "Email format invalid" |
| Transient system error | Toast (auto-dismiss 5s) | "Network blip, try again" |
| Blocking destructive failure | Dialog | "Delete failed: conflict" |
| Operation-in-progress context | Inline status | "Saving…" |

## Rationale

The Peak-End Rule (`foundations.md § Peak-End Rule`) predicts that users disproportionately remember the worst moment of a session and the final moment. A dialog over a typo is a "peak" — it becomes the memory of the form. Keeping field errors inline preserves the actual peak for genuine failures.

Jakob's Law (`foundations.md § Jakob's Law`) reinforces the toast/inline/dialog split because it is the convention across Gmail, Linear, Notion, Figma, and Superhuman. Users already know that toasts are dismissable, that inline red text is "fix this here," and that a dialog means "you can't proceed until you answer." Fighting that mapping makes every error take longer to parse.

Eagle's four-channel feedback mandate (`voices/eagle.md § Actionable principles (principle 5)`) requires four independent feedback channels — sound, toast, inline, modal — so that a user who disabled one still receives critical errors through another. Error-surface selection is the decision of which channel this specific error deserves, not whether to raise it at all.

See `_decisions-ledger.md § 2026-04-17` (item 3): inline for field, toast for transient system, dialog only for blocking destructive. Overuse of the dialog channel is one of the fastest ways to wear out user trust; reserve it.

In-progress context ("Saving…", "Uploading 3/10") is not an error but shares the inline-status surface because the user's attention is already on the artifact. Surfacing "saving" in a toast implies a handoff — that the user can walk away — which is untrue for a save that may fail.

## Applies to components

- `components/inline-edit.md` — field validation + in-progress "Saving…" inline status.
- `components/dialog.md` — hosts blocking destructive failure surfaces (e.g., new-lead-dialog save conflict, delete-failed dialog).
- `components/list-row.md` — inline status for row-level operations (e.g., lead-list row shows "Saving" during an optimistic update).
- `components/toast.md` — transient system error channel (auto-dismiss 5s per `motion.md § Duration defaults`).
- `components/command-palette.md` — inline error surface for query-level failures ("No results", "Search unavailable").

Every component that can produce errors MUST declare its error-surface strategy in its checklist.
