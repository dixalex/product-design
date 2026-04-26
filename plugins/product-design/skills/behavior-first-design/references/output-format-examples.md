# Output-format examples

Three worked invocations of the 6-step output contract: Step 1 (4 clarifying answers) → Step 2 (filled checklist) → Step 3 (citations) → Step 4 (code) → Step 5 (behavior contract) → Step 6 (self-audit).

---

## Example 1: Button

**User prompt:** *"Build me a primary action button for submitting the new-lead form."*

### Step 1 — Clarifying answers

1. **Loading state needed?** Yes. Lead submit is an async network call; the button must express pending state and suppress duplicate clicks.
2. **Icon on left, right, or none?** None. Label-only — "Create lead" is unambiguous; adding an icon inflates visual weight without clarifying intent.
3. **Primary vs secondary?** Primary. Per `foundations.md § Hick's Law`, a view has exactly one primary action; this submit is it.
4. **Disabled state?** Yes. Disabled while the form is invalid *and* while the submit is in-flight (distinct from `aria-busy`).

### Step 2 — Filled checklist (from `components/button.md § Checklist`)

```yaml
disabled_state: yes            # while form invalid OR submit in-flight
loading_state: yes             # async submit → aria-busy + spinner
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: n-a   # label-only
hover_affordance_hidden_in_idle: n-a
keyboard_activation: both      # Enter AND Space
esc_closes: n-a
validation_timing: n-a         # form owns validation
aria_busy_when_loading: yes
min_hit_target_px: 40
```

### Step 3 — Citations

- `foundations.md § Fitts's Law` — enforces the 40×40 minimum hit target; vertical padding computed to hit that floor even with a 14px label.
- `voices/linear.md § Actionable principles (principle 1)` — rank every surface by attentional weight before styling; the submit is central to the current task and carries full weight, neighboring controls recede.

### Step 4 — Code

```tsx
import { Button } from "@base-ui/react/button";
import { Spinner } from "@/ui/spinner";
import { useFormStatus } from "react-dom";

export function SubmitLeadButton({ disabled }: { disabled?: boolean }) {
  const { pending } = useFormStatus(); // pending = in-flight submit
  const isBusy = pending;
  const isDisabled = disabled || pending;

  return (
    <Button
      type="submit"
      disabled={isDisabled}
      aria-busy={isBusy || undefined}
      className={[
        "relative inline-flex items-center justify-center",
        "min-h-[40px] px-4 rounded-md",           // Fitts: 40px floor
        "bg-indigo-600 text-white text-sm font-medium",
        "hover:bg-indigo-700 active:bg-indigo-800",
        "focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-400",
        "disabled:bg-neutral-300 disabled:text-neutral-500 disabled:cursor-not-allowed",
        "transition-colors duration-100",          // ≤150ms for frequent actions
      ].join(" ")}
    >
      {/* Swap content, preserve width to avoid layout shift */}
      <span className={isBusy ? "invisible" : ""}>Create lead</span>
      {isBusy && (
        <span className="absolute inline-flex items-center gap-2">
          <Spinner size={14} /> Creating…
        </span>
      )}
    </Button>
  );
}
```

### Step 5 — Behavior contract

- Enter **and** Space activate when focused (Base UI `Button` normalizes this; never use `<div onClick>`).
- `:focus-visible` outline appears only on keyboard focus; pointer clicks never paint the ring.
- `aria-busy="true"` during submit; label swaps for a spinner of matching width — no layout shift.
- Minimum hit target 40×40 CSS pixels (`min-h-[40px]` + horizontal padding).
- Disabled removes the button from the tab order; `aria-disabled` is not used (the reason is in the error summary).
- Press feedback paints within 16ms.

### Step 6 — Self-audit

1. *Obviously interactive?* Yes — filled indigo against neutral canvas, hover darkens, label verb names the action.
2. *4.5:1 contrast in every state?* Indigo-600 on white = 4.83:1 (pass). Disabled neutral-500 on neutral-300 = 3.1:1 — **risk**; flag a darker disabled foreground token before ship.
3. *Disabled visually distinct from loading?* Yes — disabled dims the whole button; loading keeps primary color but swaps label for spinner.

---

## Example 2: Inline-edit Input

**User prompt:** *"Let users edit the lead's 'Company' field inline in the list row."*

### Step 1 — Clarifying answers

1. **Validation trigger?** On-blur. Company is a cheap local check (non-empty, ≤120 chars); on-change would flash errors while the user is still typing (`patterns/form-validation-timing.md § Decision table`).
2. **Esc cancels, Enter commits + advances?** Yes — Linear/Notion contract. Enter saves *and* moves focus to the next editable cell; Esc reverts to the last committed value.
3. **Placeholder on empty?** Yes — "Add company" as placeholder, never the word "Empty" or a dash, so the empty state is an invitation, not a label.
4. **Error surface?** Inline below the field via `aria-describedby`, with `aria-invalid="true"`. No toast for validation errors — those are reserved for server rollback (`patterns/error-surface.md`).

### Step 2 — Filled checklist (from `components/input-inline-edit.md § Checklist`)

```yaml
disabled_state: yes
loading_state: yes             # optimistic commit → background save
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: n-a
hover_affordance_hidden_in_idle: yes   # subtle underline on hover only
keyboard_activation: both              # Enter / Space / F2 to enter edit
esc_closes: yes                        # Esc reverts
validation_timing: on-blur
placeholder_on_empty: yes
autofocus_on_enter_edit: yes           # imperative focus(), not HTML autofocus
commit_on_blur: yes
commit_on_enter: yes                   # and advances focus
cancel_on_esc: yes
aria_invalid_on_error: yes
```

### Step 3 — Citations

- `foundations.md § Doherty Threshold` — the 50ms open budget for entering edit mode keeps the interaction feeling direct, not dialog-like.
- `foundations.md § Jakob's Law` — match the Linear/Notion/Gmail inline-edit keyboard contract users already know.
- `components/input-inline-edit.md § Keyboard contract` — authoritative source: Esc cancels, Enter commits + advances, Tab commits.

### Step 4 — Code

```tsx
import { Field } from "@base-ui/react/field";
import { useRef, useState } from "react";

type Status = "idle" | "editing" | "error" | "saving";

export function InlineCompanyEdit({
  value,
  onCommit,
}: { value: string; onCommit: (next: string) => Promise<void> }) {
  const [status, setStatus] = useState<Status>("idle");
  const [draft, setDraft] = useState(value);
  const [error, setError] = useState<string | null>(null);
  const inputRef = useRef<HTMLInputElement>(null);

  const enterEdit = () => {
    setStatus("editing");
    setDraft(value);
    queueMicrotask(() => { inputRef.current?.focus(); inputRef.current?.select(); });
  };

  const commit = async () => {
    const trimmed = draft.trim();
    if (trimmed.length === 0 || trimmed.length > 120) {
      setError(trimmed.length === 0 ? "Company is required." : "Max 120 characters.");
      setStatus("error");
      return;
    }
    setError(null);
    setStatus("saving");                 // optimistic — UI reflects trimmed now
    try { await onCommit(trimmed); setStatus("idle"); }
    catch { setStatus("error"); setError("Couldn't save. Try again."); }
  };

  const cancel = () => { setDraft(value); setError(null); setStatus("idle"); };

  if (status === "idle" || status === "saving") {
    return (
      <button
        type="button"
        onClick={enterEdit}
        onKeyDown={(e) => { if (e.key === "Enter" || e.key === " " || e.key === "F2") enterEdit(); }}
        className="text-left w-full px-2 py-1 rounded hover:underline focus-visible:outline-2 focus-visible:outline-indigo-400"
      >
        {value || <span className="text-neutral-400">Add company</span>}
      </button>
    );
  }

  return (
    <Field.Root invalid={status === "error"}>
      <input
        ref={inputRef}
        value={draft}
        onChange={(e) => setDraft(e.target.value)}
        onBlur={commit}
        onKeyDown={(e) => {
          if (e.key === "Enter") { e.preventDefault(); commit(); /* parent advances focus */ }
          if (e.key === "Escape") { e.preventDefault(); cancel(); }
        }}
        aria-invalid={status === "error" || undefined}
        aria-describedby={error ? "company-err" : undefined}
        className="w-full px-2 py-1 rounded border border-indigo-400 focus:outline-none focus:ring-2 focus:ring-indigo-300 aria-[invalid=true]:border-red-500"
      />
      {error && <Field.Error id="company-err" className="text-xs text-red-600 mt-1">{error}</Field.Error>}
    </Field.Root>
  );
}
```

### Step 5 — Behavior contract

- Idle cell renders as text, not a field; hover adds a subtle underline — no border or box.
- Click / Enter / Space / F2 enters edit mode in <50ms via imperative `focus()` + `select()` — never HTML `autofocus`.
- Validation fires on-blur and on Enter, never on-change. Errors flip `aria-invalid="true"` and keep the input open.
- Enter commits and (upstream) advances to the next cell; Esc reverts to the last committed value.
- Commit is optimistic; server failure surfaces via undo toast (`patterns/error-surface.md`), not an in-row error.
- On exit, focus returns to the cell button — never `<body>` (`keyboard-and-focus.md § On content removed`).

### Step 6 — Self-audit

1. *Handles server rollback?* Partially — `onCommit` rejection sets `error`, but the row won't un-set its *parent's* optimistic value. Flag: parent row should own the optimistic cache and pass a rollback callback.
2. *`aria-invalid` cleared on recovery?* Yes — `status === "error"` drives it; a successful re-commit resets to `"idle"` and the attribute drops.
3. *50ms open budget feels right?* `queueMicrotask` fires focus sub-ms, well under 50ms.

---

## Example 3: Command palette wrapper

**User prompt:** *"Add a Cmd-K command palette for navigating leads and running actions."*

### Step 1 — Clarifying answers

1. **Top-level commands?** Four, per `foundations.md § Miller's Law` (Cowan ~4): "New lead", "Search leads", "Switch view", "Settings". Anything else hides behind sub-panes or typing.
2. **Sub-commands?** Yes — "New lead" pushes a sub-pane for the field stub (name + company). Esc/Backspace-on-empty pops back to root.
3. **Recent items?** Yes — last 4 leads visited, grouped under "Recent" above the command list when the input is empty.
4. **Theme?** Matches `visual-design/references/tokens/operate-extracted.md` — neutral-900 surface, indigo-400 selection highlight, 6px radius, 120ms enter motion.

### Step 2 — Filled checklist (from `components/command-palette.md § Checklist`)

```yaml
disabled_state: yes
loading_state: yes
focus_visible_only_on_keyboard: yes
icon_only_has_accessible_name: yes
keyboard_activation: Enter
esc_closes: yes
esc_goes_back_in_sub_pane: yes
validation_timing: n-a
summon_shortcut: cmd+k
summon_shortcut_mac_and_windows: yes
typeahead_match_type: substring
recents_shown_before_typing: yes
top_list_visible_count: 4
sub_commands_supported: yes
palette_first_paint_ms: 50
enter_motion_ms: 120
reduced_motion_enter_ms: 0
trap_focus: yes
portal_to_body: yes
```

### Step 3 — Citations

- `foundations.md § Jakob's Law` — ⌘K is the cross-product default (Linear, Superhuman, Raycast, Vercel). Users arrive with this expectation; match it exactly.
- `response-time-budget.md § Numeric targets` — ⌘K → palette visible: 50ms P95, 100ms outer bound. The registry preloads on mount so open is a visibility flip, never "summon then fetch".
- `voices/superhuman.md § Actionable principles (principle 1)` — 100ms ceiling, 50ms ambition, 32ms render target: the ethos behind defending the 50ms budget, not settling for 100ms.

### Step 4 — Code

```tsx
import { Command } from "cmdk";
import { Dialog } from "@base-ui/react/dialog";
import { useEffect, useState } from "react";

type Pane = "root" | "new-lead";

export function CommandPalette({ recents }: { recents: { id: string; name: string }[] }) {
  const [open, setOpen] = useState(false);
  const [pane, setPane] = useState<Pane>("root");
  const [input, setInput] = useState("");

  useEffect(() => {
    const onKey = (e: KeyboardEvent) => {
      if ((e.metaKey || e.ctrlKey) && e.key.toLowerCase() === "k") {
        e.preventDefault(); setOpen((o) => !o);
      }
    };
    window.addEventListener("keydown", onKey);
    return () => window.removeEventListener("keydown", onKey);
  }, []);

  const popPane = () => { setPane("root"); setInput(""); };

  return (
    <Dialog.Root open={open} onOpenChange={setOpen}>
      <Dialog.Portal>
        <Dialog.Backdrop className="fixed inset-0 bg-black/40 data-[entering]:duration-[120ms]" />
        <Dialog.Popup className="fixed left-1/2 top-[20vh] -translate-x-1/2 w-[640px] rounded-lg bg-neutral-900 text-neutral-100 shadow-2xl overflow-hidden data-[entering]:animate-in data-[entering]:fade-in data-[entering]:zoom-in-95 motion-reduce:animate-none">
          <Command
            label="Command palette"
            shouldFilter={true}
            onKeyDown={(e) => {
              if (pane !== "root" && (e.key === "Escape" || (e.key === "Backspace" && input === ""))) {
                e.preventDefault(); popPane();
              }
            }}
          >
            <Command.Input
              value={input}
              onValueChange={setInput}
              placeholder={pane === "root" ? "Type a command or search…" : "Lead name…"}
              className="w-full px-4 py-3 bg-transparent text-sm outline-none border-b border-neutral-800"
            />
            <Command.List className="max-h-[360px] overflow-y-auto p-2" aria-live="polite">
              <Command.Empty className="px-3 py-6 text-center text-neutral-500 text-sm">No results.</Command.Empty>

              {pane === "root" && (
                <>
                  {input === "" && recents.length > 0 && (
                    <Command.Group heading="Recent">
                      {recents.slice(0, 4).map((l) => (
                        <Command.Item key={l.id} value={`recent ${l.name}`} onSelect={() => { /* navigate */ setOpen(false); }}>
                          {l.name}
                        </Command.Item>
                      ))}
                    </Command.Group>
                  )}
                  <Command.Group heading="Actions">
                    <Command.Item onSelect={() => setPane("new-lead")}>New lead →</Command.Item>
                    <Command.Item onSelect={() => { /* focus search */ setOpen(false); }}>Search leads</Command.Item>
                    <Command.Item onSelect={() => { /* open view picker */ setOpen(false); }}>Switch view</Command.Item>
                    <Command.Item onSelect={() => { /* settings */ setOpen(false); }}>Settings</Command.Item>
                  </Command.Group>
                </>
              )}

              {pane === "new-lead" && (
                <Command.Group heading="New lead">
                  <Command.Item onSelect={() => { /* create with input as name */ setOpen(false); popPane(); }}>
                    Create "{input || "…"}"
                  </Command.Item>
                </Command.Group>
              )}
            </Command.List>
          </Command>
        </Dialog.Popup>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

### Step 5 — Behavior contract

- ⌘K / Ctrl+K toggles from anywhere; the handler is mounted once at window scope.
- First paint ≤50ms: the `Command` tree is mounted-but-hidden by Dialog, so open is a visibility flip, not a fetch.
- Substring filter is the cmdk default; "new lead" matches "Create New Lead" — no prefix lockout.
- Empty input shows Recents (≤4) + Actions (4). Total default rows = 4 + 4, keeping the Miller's Law budget.
- Sub-pane via `pane` state: Enter on "New lead →" pushes; Esc or Backspace-on-empty pops. Focus stays on the input throughout.
- Enter motion is 120ms (`motion.md § Duration defaults`); `motion-reduce:animate-none` collapses it for users who set the preference.
- `aria-live="polite"` on the list announces result-count changes to assistive tech after the typing pause.

### Step 6 — Self-audit

1. *⌘K paints <50ms?* Yes — visibility flip of an already-mounted tree. If we later lazy-load the `cmdk` bundle on first open, this claim breaks.
2. *Recents from local state?* Yes — `recents` is a prop; parent must hydrate from `localStorage` or store at mount, not on ⌘K.
3. *Typeahead case-insensitive?* Yes — cmdk's default `filter` lowercases both sides; substring + fuzzy scoring is default.
