# Structure

Second layer. Defines the *objects* in the product, the *surfaces* that host them, and the *hierarchy* those surfaces sit in. Depends on Intent (Q2's JTBD statement seeds the nouns). The most heavily canon-supported of the four layers.

## Canon

- **Brown's 8 IA Principles.** Primary — see `canon/brown-8-ia-principles.md`. Principles 1 (Objects), 2 (Choices), 6 (Multiple Classification), 7 (Focused Navigation), 8 (Growth) all fire here.
- **OOUX — Objects before actions.** The methodology that bridges Intent → Structure via noun-extraction. See `canon/ooux-objects-before-actions.md`.
- **Polaris — "one home, many doors"** and "plan for growth." See `canon/polaris-ia-foundations.md`.
- **Hick's Law.** Number of choices at each navigation level drives decision time. Cross-ref `behavior-first-design/references/foundations.md § Hick's Law`.
- **Miller's Law.** Group items into chunks of 7±2 per nav level. Cross-ref `behavior-first-design/references/foundations.md § Miller's Law`.
- **Jakob's Law.** Users spend most of their time on *other* products; match the conventions they already know for the domain. Cross-ref `behavior-first-design/references/foundations.md § Jakob's Law`.
- **OOUX/ORCA Round 3 — Prioritization (the collapse step).** See `canon/ooux-objects-before-actions.md § Round 3`.
- **Daniel Jackson — *The Essence of Software*** (MIT Press, 2021). Cognitive vs business objects; concepts ARE the software. See `canon/jackson-essence-of-software.md`.
- **Verb-back-derivation precedent** — Linear (Issue), Heroku (App). Pick the smallest noun that carries the constant verb. (No dedicated canon file; the discipline is captured inline at Q0a.)

## Required output

Feeds the `## Structure` section of the handoff spec. See `handoff-spec-template.md`.

- `objects` — list of 3–7 primary entities. Each entry is `<name>` + role (`primary` | `container` | `dependent`) + key properties + states.
- `surfaces` — named surfaces (e.g. `Inbox`, `PipelineBoard`, `LeadDetail`). Each entry: purpose (one line), primary object it hosts, views it supports.
- `hierarchy` — sidebar items, top-bar items, per-surface local actions. The global chrome the product ships with.

## Dialogue questions

Asked sequentially, one at a time. The skill MUST complete Q0a → Q1.5 before moving to Q2; this collapse-first sequence is what produces a derived object model rather than a peer-mimicked one. The methodology synthesis lives at `pine-research/research/product-architecture-object-derivation/methodology.md` and the worked CRM application at `pine-research/research/product-architecture-object-derivation/crm-test-case-application.md` — load them when in doubt.

1. **"What's the constant verb the user does when working this product? Read the JTBD aloud, find the action — 'move leads forward,' 'edit the document,' 'deploy the app.' Now: what's the smallest noun that carries that verb? Mark it `primary`."** *(Q0a — verb-back-derivation)*
   - Expected shape: 1 verb + 1 primary-candidate noun.
   - Precedent: Linear (Issue), Heroku (App). See `pine-research/research/product-architecture-object-derivation/linear-notion-figma-heroku-derivation-stories.md` for the full derivation stories.
2. **"(a) When the user thinks aloud about doing this work, what nouns do they say in their head — descriptive, not technical? (b) What does their team / industry call those things in shared vocabulary?"** *(Q0b — cognitive vs business reconciliation)*
   - Expected shape: two parallel lists (cognitive nouns | business labels) with a 1:1 mapping where they align, and an explicit note where they diverge.
   - The internal object model uses cognitive nouns; UI labels follow Jakob's Law (use business labels if the user expects them). See `canon/jackson-essence-of-software.md`; adjacent: Indi Young, *Practical Empathy* (Rosenfeld Media, 2015).
3. **"List the candidate objects from the JTBD + persona + scope-as-feature exclusions. Apply Prater's three filters: nouns that recur across sources; nouns with structure (multiple attributes + meaningful detail page); nouns with instances + purpose."** *(Q1 — Noun foraging, reframed)*
   - Expected shape: bulleted list, 5–12 candidates pre-collapse.
   - Skill pre-seeds by running Prater's filters on the Intent block (JTBD + persona + scope) and offers the extracted nouns as a starting point the user can edit.
4. **"For every candidate pair, apply Prater's four collapse verbs. Default is 'no fork unless empirically justified.'"** *(Q1.5 — Collapse pass; REQUIRED gate before Q2)*
   - **Combine** when: most attributes shared, most CTAs shared, always change together.
   - **Downgrade** when: <3 attributes, no detail-page demand → make it an attribute of another object.
   - **Offload** when: belongs to a different domain or a later phase.
   - **Eliminate** when: exists in domain vocabulary but no real CTA touches it.
   - **State-field collapse** when: two candidates are the same primitive in different states (Linear's Issue, Notion's Block — same noun, multiple states).
   - Expected shape: per-pair table — `pair | share attributes? | always change together? | verb | 1-line rationale`. The skill emits the table from `pine-research/research/product-architecture-object-derivation/crm-test-case-application.md § Step 5` as the worked-example shape.
5. **"How do those objects relate? Which are peers, which are containers, which are dependents?"** *(Q2 — Relationships)*
   - Expected shape: per object, one of `primary` / `container` / `dependent` + one-line relationship note (e.g. "Pipeline contains Leads; Activity depends on Lead").
6. **"Which object does the user start on? That's your primary surface."** *(Q3 — Primary surface)*
   - Expected shape: one object name. Becomes the anchor surface.
7. **"What views should the primary surface support? Default by the verb identified in Q0a — list (work-my-queue), grid (browse), detail-canvas (compose), dashboard (monitor). Domain peers cited as convention precedent (Jakob's Law) AFTER the verb-derived proposal, not as the source."** *(Q4 — Views, rewritten)*
   - Expected shape: 1–3 view names ranked by default.
8. **"What goes in the sidebar vs. top bar vs. per-surface local actions?"** *(Q5 — Hierarchy)*
   - Expected shape: three short lists. Sidebar = object navigation; top bar = utility/context; per-surface = actions on the object in view.
   - Skill proposes a hierarchy that satisfies Brown #7 (Focused Navigation) and Miller's Law (≤7 sidebar items). Domain peers may be cited as precedent for chrome conventions, but the proposal is derived from the object model in Q1.5 + Q2, not copied from peers.
9. **"Now compare the derived object model and hierarchy against 2–3 domain peers (the ones named in Intent's Q5). For every deviation, name it explicitly: 'deliberate opinion you'd defend' or 'to revisit v1.1.' If you can't name the deviation, the model isn't done."** *(Q5.5 — Peer comparison as sanity check)*
   - Expected shape: per-peer table — `peer | their model | our deviation | deliberate?`. Worked example: `pine-research/research/product-architecture-object-derivation/crm-test-case-application.md § Step 7`.

## Options pattern

Structure differs from Flows/Disclosure in that **derivation produces the options**, not peer-mimicry. The skill does NOT propose peer-shapes as candidate object models. Instead:

**For Q4 (primary-surface views)** — keyed to the verb identified in Q0a:

- "work my queue / process my list" → **list** (default), **kanban** (runner-up)
- "browse / explore" → **grid** (default), **list** (runner-up)
- "compose / edit" → **detail-canvas** (default), **split-detail** (runner-up)
- "monitor / oversee" → **dashboard** (default), **timeline** (runner-up)

The skill cites domain peers as **convention precedent** (Jakob's Law) AFTER proposing the verb-derived view, not as the source. E.g.: "verb is 'work my queue' → list-default; for reference, Linear and Superhuman use the same pattern."

**For Q5 (sidebar vs. top bar vs. per-surface)** — neutral; skill proposes a hierarchy that satisfies Brown #7 (Focused Navigation) and Miller's Law (≤7 sidebar items), then cites how 1–2 domain peers structure the same chrome.

**For Q5.5 (peer comparison as sanity check)** — see `pine-research/research/product-architecture-object-derivation/crm-test-case-application.md § Step 7` for the worked deviation table format. The skill explicitly asks the user to name each deviation as "deliberate opinion" or "to revisit v1.1." Unnamed deviations fail the gate.

The cross-domain surprise (e.g., "Figma's pages-within-files applied to your CRM") is preserved as an optional Q4 prompt, but only after the verb-derived default has been proposed.

## Principles invoked

- **OOUX/ORCA Round 3 (Prater):** Q1.5 is literally the four collapse verbs as a question. See `canon/ooux-objects-before-actions.md § Round 3`.
- **Cognitive-vs-business object distinction (Jackson):** Q0b is the bridge between Intent's JTBD nouns and Structure's object model. The cognitive list wins for the internal model; business labels apply at the UI layer per Jakob's Law. See `canon/jackson-essence-of-software.md`.
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
- [ ] **Verb-back-derivation completed** (Q0a): primary verb named, primary noun chosen.
- [ ] **Cognitive-vs-business reconciliation completed** (Q0b): cognitive nouns + business labels documented; mapping noted where they diverge.
- [ ] **Collapse pass applied** (Q1.5): each candidate pair has an explicit verb (Combine/Downgrade/Offload/Eliminate/state-field) + 1-line rationale.
- [ ] **Peer-comparison sanity check ran AFTER derivation** (Q5.5): every deviation named "deliberate opinion" or "to revisit v1.1."
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
