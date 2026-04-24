# Form validation timing

Pick the moment validation fires by the cost of the check and the cost of a false positive. Cheap checks wait until the field blurs. Expensive checks (network, cryptographic) debounce during typing so the user sees results before submit. Destructive and security-sensitive submits re-validate only at submit time — during typing, stay silent.

## Decision table

| Condition | Rule | Example |
|---|---|---|
| Field with expensive check (password strength, uniqueness) | on-change (debounced 300ms) | Username uniqueness |
| Field with inexpensive check | on-blur | Required field, email format |
| Destructive submit | on-submit final validation | Delete confirmation |
| Login / security | on-submit only (don't leak info) | Password |

## Rationale

Nielsen's 1-second flow boundary (`foundations.md § Nielsen's Response Time Triad`) and the Doherty Threshold (`foundations.md § Doherty Threshold`) together set the budget: feedback must arrive inside Nielsen's 1s flow boundary. A 300ms debounce on a remote uniqueness check lands inside that window on typical broadband; tighter debounces spam the network for no perceptual gain, looser ones feel broken.

On-blur for inexpensive checks respects Norman's affordances (`foundations.md § Norman's Affordances + Signifiers`): the user signals "I'm done with this field" by leaving it. Validating mid-typing — before the user is done — punishes intent before the intent is complete. Error text that appears while the user is still forming the value reads as accusatory.

Destructive submits hold validation until submit so the user can still back out. Showing "this will delete 47 records" mid-typing is noise; showing it at the submit boundary is signal. Security fields (password, 2FA) must not validate pre-submit because doing so leaks information to an on-looker or a script — the only acceptable signal is "correct" or "incorrect" after the full credential is offered.

See `_decisions-ledger.md § 2026-04-17` (item 2) for the project-level ruling that on-blur is the default, on-change is the exception for expensive checks, and on-submit is reserved for destructive and security flows.

## Applies to components

Pattern is invoked by every form-bearing component in the Common 15:

- `components/input.md` / `components/inline-edit.md` — individual field timing (on-blur default; on-change only when an expensive check is declared).
- `components/dialog.md` — new-lead-dialog, settings dialog, sign-up dialog compose multiple inputs and own the submit-time aggregate pass.
- `components/command-palette.md` — query-style inputs validate on-change because the expensive work (search) is already debounced.
- `components/combobox.md` — remote-search combobox follows the on-change-debounced-300ms rule.

Any component that wraps an input MUST declare its validation timing in its checklist (see `_templates.md § Component template`, field `validation_timing`).
