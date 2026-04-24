# Structure

Second layer. Defines the *objects* in the product, the *surfaces* that host them, and the *hierarchy* those surfaces sit in. Depends on Intent (Q2's JTBD statement seeds the nouns). The most heavily canon-supported of the four layers.

## Canon

- **Brown's 8 IA Principles.** Primary — see `canon/brown-8-ia-principles.md`. Principles 1 (Objects), 2 (Choices), 6 (Multiple Classification), 7 (Focused Navigation), 8 (Growth) all fire here.
- **OOUX — Objects before actions.** The methodology that bridges Intent → Structure via noun-extraction. See `canon/ooux-objects-before-actions.md`.
- **Polaris — "one home, many doors"** and "plan for growth." See `canon/polaris-ia-foundations.md`.
- **Hick's Law.** Number of choices at each navigation level drives decision time. Cross-ref `behavior-first-design/references/foundations.md § Hick's Law`.
- **Miller's Law.** Group items into chunks of 7±2 per nav level. Cross-ref `behavior-first-design/references/foundations.md § Miller's Law`.
- **Jakob's Law.** Users spend most of their time on *other* products; match the conventions they already know for the domain. Cross-ref `behavior-first-design/references/foundations.md § Jakob's Law`.

## Required output

Feeds the `## Structure` section of the handoff brief. See `handoff-brief-template.md`.

- `objects` — list of 3–7 primary entities. Each entry is `<name>` + role (`primary` | `container` | `dependent`) + key properties + states.
- `surfaces` — named surfaces (e.g. `Inbox`, `PipelineBoard`, `LeadDetail`). Each entry: purpose (one line), primary object it hosts, views it supports.
- `hierarchy` — sidebar items, top-bar items, per-surface local actions. The global chrome the product ships with.

## Dialogue questions

Asked sequentially. The skill uses Q1's answer as the seed for Q2–Q5.

1. **"List the 3–7 primary objects in this product. Nouns only, no verbs yet. (e.g. for a CRM: Lead, Pipeline, Activity.)"**
   - Expected shape: bullet list, 3–7 items.
   - Skill pre-seeds by running OOUX noun-extraction on the Intent JTBD statement and offers the extracted nouns as a starting point the user can edit.
2. **"How do those objects relate? Which are peers, which are containers, which are dependents?"**
   - Expected shape: per object, one of `primary` / `container` / `dependent` + one-line relationship note (e.g. "Pipeline contains Leads; Activity depends on Lead").
3. **"Which object does the user start on? That's your primary surface."**
   - Expected shape: one object name. Becomes the anchor surface.
4. **"What views should the primary surface support?"** — skill proposes 2–3 options grounded in domain peers (list / kanban / calendar / timeline / grid) plus an optional cross-domain surprise.
   - Expected shape: 1–3 view names, ranked by default.
5. **"What goes in the sidebar vs. top bar vs. per-surface local actions?"** — skill proposes 2–3 options grounded in domain peers.
   - Expected shape: three short lists. Sidebar = object navigation; top bar = utility/context; per-surface = actions on the object in view.

## Options pattern

Worked example — user's domain is CRM, domain peers are Linear + Close + Attio.

**For Q4 (primary-surface views):**

- **Option A: Linear's shape — peer objects, single-list primary surface, no sub-navigation.** The primary surface is one long list of issues; filters and saved views provide the re-slicing. Sidebar is all top-level destinations (Inbox, My Issues, Projects); cmd-K handles everything else. Best when the user's day is spent *in* the list.
- **Option B: Close's shape — primary lead + dependent activity inline.** The primary surface is a lead detail with the email thread, call log, and activity timeline inlined into the right rail. Sidebar is inbox-style (Unread, Today, Queue). Best when the workflow is high-volume reply triage and the activity *is* the lead.
- **Option C: Attio's shape — flexible schemas, user-defined objects.** The primary surface is a user-defined list/table; the object model is editable at runtime. Sidebar is workspace-scoped. Best when the user's mental model of "what a lead is" differs materially per team.
- **Cross-domain surprise:** Figma's pages-within-files — each Lead is a "file," each Activity is a "page" inside that file. Radical for CRM, high-upside if the user has dense per-lead context. Rarely the default.

**For Q5 (sidebar vs. top bar vs. per-surface):**

The three peer shapes above drive three candidate nav layouts. The skill proposes the peer whose primary workflow matches the user's Q1 (objects) and Q3 (primary surface) answers, and one alternative for comparison. Cross-domain surprise is offered only if the user explicitly requests it.

## Principles invoked

- **Brown #1 (Objects):** Q1 is literally Brown's Objects principle as a question.
- **Brown #2 (Choices):** Q4 — each surface must serve a focused task. If the user proposes a surface that serves two purposes, the skill surfaces the conflict and asks for a split. Cross-link: `behavior-first-design/references/foundations.md § Hick's Law`.
- **Brown #6 (Multiple Classification):** Q4 + Q5 — same objects, multiple views (lens) and multiple navigation axes (tags, statuses, saved views).
- **Brown #7 (Focused Navigation):** Q5 — don't mix objects, actions, and utility in a single nav scheme. The sidebar is for one kind of thing per region.
- **Brown #8 (Growth):** Final Q5 sanity-check — "does this hierarchy survive 10× growth in objects?"
- **OOUX (Objects before actions):** Q1 is pure OOUX. Q2 is OOUX object-mapping (containers/peers/dependents).
- **Polaris "one home, many doors":** Q3 + Q5 — each object has one canonical surface and multiple entry paths into that surface. The entry paths are Flows work (next layer); this layer guarantees the home exists.
- **Miller's Law:** Q5 — sidebar items capped at ~7 (nav chunking at the first level); if the user's list exceeds that, the skill proposes a collapse/grouping move. Cross-link: `behavior-first-design/references/foundations.md § Miller's Law`.
- **Jakob's Law:** Q4 + Q5 — the proposed options are *grounded in the user's domain peers* precisely to honor convention-matching. Cross-link: `behavior-first-design/references/foundations.md § Jakob's Law`.

## Gate criteria

The skill does NOT proceed to Flows until:
- [ ] 3–7 objects listed, each with role (primary/container/dependent) + one-line properties (Q1 + Q2).
- [ ] Primary surface named (Q3).
- [ ] At least one view chosen per primary surface (Q4); runners-up noted as "optional."
- [ ] Hierarchy sketched — sidebar items, top-bar items, per-surface local actions (Q5).
- [ ] Sidebar item count ≤ 7 (or the user has explicitly acknowledged and justified exceeding Miller's Law).
- [ ] User has confirmed the Structure block reads correctly.

## Transition prompt

The skill MUST emit this sentence verbatim once the gate criteria are met:

> **"Structure captured. Ready to move to Flows, or should I revise? (yes / revise)"**

If `revise`, the skill re-opens whichever sub-question the user wants to revise. If `yes`, the skill moves to Flows — see `layers/flows.md`.
