# Intent

First layer. Captures *what this product is for and who it's for*, before any structure, flows, or disclosure decisions. Garrett's "Strategy" plane, expressed as a JTBD statement the user brings (or the skill extracts).

## Canon

- **JTBD (Christensen).** "When [situation], I want [motivation], so I can [expected outcome]." See `canon/jtbd-christensen.md`.
- **IDEO 3-lens.** Desirability × Feasibility × Viability — an Intent read that's complete only when all three hold.
- **Linear's "Direction" framing.** Karri Saarinen, "Set the product direction": direction as "who we're for and what we're solving," cascaded into project pitches. https://linear.app/method/introduction.
- **Eagle §Purpose (Pine internal).** "The domain logic layer IS the product." Opinionated defaults for a specific user beat configurable everything for everyone.
- **Scope-as-a-feature (Saarinen).** "The simplest way to increase quality is to reduce scope." Intent declares what the product will *not* do as explicitly as what it will. https://www.figma.com/blog/karri-saarinens-10-rules-for-crafting-products-that-stand-out/.

## Required output

Feeds the YAML front-matter and the `## Intent` prose block of the handoff brief. See `handoff-brief-template.md`.

- `jtbd` — the JTBD statement (one sentence, canonical form).
- `persona` — one-line description of the primary user (role, scale, context).
- `primary_job` — the one-line essence of the job, for cross-layer reference.
- `desirable × feasible × viable` — three short paragraphs, one per lens.
- `domain` — one of: `crm`, `productivity`, `dashboard`, `mobile`, `dev-tool`, `design-tool`, `media`, `feed`, `other`.
- `domain_peers` — 2–3 products the user (or the `domains.md` fallback) considers reference-quality.

## Dialogue questions

Asked sequentially, one per skill message. The skill waits for the user's answer before asking the next question.

**Recommendation discipline:** every question must lead with a recommended default if any context is available. Even at Intent — where the skill extracts rather than proposes the JTBD itself — the skill SHOULD propose 2–3 sharper framings when the user's answer is fuzzy or generic, and SHOULD infer-then-confirm rather than asking cold whenever upstream context (the user's initial prompt) gives a signal.

1. **"Give me the one-sentence version: who is this for, and what job do they hire it to do?"**
   - Expected shape: free-form, 1–2 sentences.
   - **Sharpening pattern (mandatory):** if the user's prompt that triggered this skill already names a product type / domain (e.g. *"design a CRM"*, *"design a Postgres admin"*), the skill MUST propose 2–3 sharper framings drawn from the relevant domain peers and ask the user to pick or override. Example for `"design a CRM"`: *"My lean — pick one or write your own: (A) a triage-first CRM like Superhuman, optimized for clearing the inbox; (B) a relationship-graph CRM like Folk, optimized for tracking who-knows-who; (C) a kanban-first CRM like Pipedrive, optimized for pipeline visibility."* The user's answer becomes Q1's answer.
   - If the user's prompt was bare and gives no signal, the skill asks open-ended.
2. **"Say that as a JTBD: *'When [situation], I want [motivation], so I can [expected outcome].'*"**
   - Expected shape: one sentence, canonical JTBD form.
   - **Sharpening pattern:** if the user's prose doesn't fit JTBD form, the skill proposes a candidate JTBD ("How about: *'When triaging new leads in the morning, I want to qualify and assign in under 30 seconds, so I can clear my inbox without losing context.'* — keep, refine, or rewrite?") rather than asking the user to format it themselves.
3. **"Who's the primary persona? Role, scale, tool-adjacent?"**
   - Expected shape: 1–2 sentences. "Scale" = solo / small team / enterprise. "Tool-adjacent" = what tools they currently use for this job.
   - **Sharpening pattern:** if the user names a generic role ("salespeople"), the skill proposes 2 narrower personas drawn from the domain peers ("a Superhuman-shaped sales-ops generalist running 50–200 leads/day, OR a Close-shaped account executive running fewer-but-deeper deals — which?") and asks for a pick or override.
4. **"Which domain is this closest to: `crm`, `productivity`, `dashboard`, `mobile`, `dev-tool`, `design-tool`, `media`, `feed`, or `other`?"**
   - Expected shape: single token from the list, or `other` + free-form. Reference: `domains.md`.
   - **Inference pattern:** the skill must INFER the domain from the user's prior answers and propose it. Example: after Q1–Q3 establish "lead-tracking CRM modeled after Linear and Close," the skill says *"Inferring domain = `crm`. Confirm or override?"* rather than asking cold.
5. **"Which 2–3 products in this domain do you consider reference-quality? I'll anchor all later options to those."**
   - Expected shape: 2–3 product names.
   - **Fallback:** if the user says "I don't know" or declines to name peers, the skill proposes 2–3 canonical peers from `domains.md` for the user's domain and asks: "OK to anchor on these? (yes / I'll name my own)." If `yes`, those become `domain_peers` for the rest of the session. If `I'll name my own`, user supplies their own. At any later gate, the user can still override peers per-layer.
6. **"Desirability / feasibility / viability read — any red flags in the 3-lens?"**
   - Expected shape: three short paragraphs, one per lens. A "no red flags" answer is acceptable but the skill presses briefly for at least one trade-off the user has already considered.

## Options pattern

Intent is user-authority on the *content* of the JTBD. The skill does not propose alternative Intents wholesale — it does not say "your real intent should be X." But the skill DOES propose **sharpening framings** at every question (see Dialogue questions above). The distinction:

- **Generative proposals (Structure / Flows / Disclosure):** skill says "here are 3 options; my lean is B because...". User picks the option's substance.
- **Sharpening proposals (Intent):** skill says "your answer was 'a CRM' — 3 sharper framings: triage-first / relationship-graph / kanban-first. Pick or override." User's choice still defines the Intent; the skill just tightens the lens.

The one extraction-only behavior: if Q1 contradicts Q2's JTBD statement (e.g., Q1 says "for sales teams" but Q2's situation is "when a developer is reviewing a PR"), the skill flags the contradiction and asks the user to reconcile — it does not paper over the mismatch.

## Principles invoked

- `canon/jtbd-christensen.md` — JTBD statement shape (Q2), 3-lens read (Q6).
- `canon/ooux-objects-before-actions.md` — noun-extraction from the Q2 JTBD statement seeds Structure. No Structure-gate output at Intent, but the skill previews the extracted nouns so the user can sanity-check them.
- Linear Method (Q1, Q3) — "who we're for and what we're solving."
- Saarinen's "design for someone, not everyone" — rejects Q3 personas that read as "everyone who uses [category]."

## Gate criteria

The skill does NOT proceed to Structure until:
- [ ] JTBD statement is written in canonical form (Q2 answered).
- [ ] Persona is one-line and specific (Q3 answered).
- [ ] Domain is named from the `domains.md` list or `other` + user override (Q4 answered).
- [ ] Domain peers are supplied — either user-named (Q5) OR accepted from the `domains.md` fallback.
- [ ] 3-lens read exists (Q6 answered, even if briefly).
- [ ] User has confirmed the Intent block reads correctly.

## Transition prompt

The skill MUST emit this sentence verbatim once the gate criteria are met:

> **"Intent captured. Ready to move to Structure, or should I revise? (yes / revise)"**

If the user answers `revise`, the skill re-opens whichever sub-question the user wants to revise. If `yes`, the skill moves to Structure — see `layers/structure.md`.
