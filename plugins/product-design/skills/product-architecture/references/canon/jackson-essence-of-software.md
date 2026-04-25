# Jackson — The Essence of Software

- **Author/source:** Daniel Jackson, *The Essence of Software: Why Concepts Matter for Great Design*, MIT Press, 2021. Ongoing essays at https://essenceofsoftware.com.
- **Statement:** The user-facing concepts ARE the software. Most products fail because business-process objects (Lead, Deal) get reified when the user's actual concept is "a person I'm pursuing + our conversation + the next move." Cognitive objects ≠ business objects; map both, then choose which the internal model follows.
- **Applies to layer:** Structure (primary). Bridges Intent → Structure where Garrett's planes and OOUX's noun-extraction stop short.
- **Why it matters:** Don Norman names the principle ("match the user's conceptual model") in *The Design of Everyday Things* but does not prescribe a method to find that model. Indi Young's Mental Models surfaces gaps and opportunities, not entities. Jackson ships the bridge: design the *concepts* the user holds in their head, treating them as the actual primitives the software is made of — not a layer above the software, not a reified industry vocabulary.
- **How the skill uses it:** Q0b in `layers/structure.md` is a literal application of Jackson's pair of questions — what nouns does the user say in their head (cognitive), and what does their industry call those things (business). The cognitive list wins for the internal object model; the business list provides UI labels per Jakob's Law.

## Full principle(s)

### Concepts are the software

Jackson's central inversion: most software thinking puts a "conceptual model" *above* the implementation. Designers diagram the user's mental model in research, then engineers build a separate object model in code, then designers translate between them. Jackson argues this separation is incoherent. The user-facing concepts — the things the user can name, manipulate, and reason about — are not above the software; they are the software. Everything else is incidental implementation.

The implication for object derivation: don't ask "what shape do peer products have?" or "what does the industry call this?" Ask "what concepts must exist for the user's job to be doable at all?" Strip away every concept that isn't load-bearing for the user's mental model. What remains is the object model.

### Cognitive vs business naming

Jackson distinguishes two layers of naming:

- **Cognitive names** — what the user actually says in their head. Often pronouns or descriptive phrases: "the deal I'm pushing," "the person I'm pursuing," "the next move I need to make."
- **Business names** — industry-standard vocabulary. "Lead," "Account," "Opportunity," "Activity."

Most software projects start with the business names because they're already documented (in CRMs to copy from, in stakeholder interviews, in PRDs). This pulls the object model toward incumbent shapes regardless of whether they fit the user's cognitive model.

Jackson's prescription, restated for the skill: **the cognitive list wins for the internal object model.** Business names apply at the UI label layer if and only if Jakob's Law dictates (the user expects them because peer products have trained them). When the two diverge, name the divergence and decide consciously.

### The reification trap

Jackson's most quoted critique: business-process objects get *reified* — treated as if they have inherent reality — when the user's actual concept is something simpler. The CRM example is canonical: Salesforce's Lead-vs-Contact-vs-Opportunity bifurcation reflects sales-org workflow, not a solo founder's cognitive model. The solo founder thinks "the person I'm pursuing + what we've talked about + what's next." Three concepts, not five. Reifying the five-object model adds friction without earning its weight.

The skill's job at Q0b is to surface the user's cognitive list explicitly, *before* the candidate object list gets contaminated by industry vocabulary. The collapse pass at Q1.5 then has the cognitive list as ground truth.

### Worked example — CRM Activity vs Task

From the test case at `pine-research/research/product-architecture-object-derivation/crm-test-case-application.md`:

The cognitive question — *"do you experience history and todos as one list or two?"* — is load-bearing. If the user answers "one list" (cognitive collapse), Activity-with-state covers both. If the user answers "two separate things in my head" (cognitive split), Activity and Task must remain peer objects. Both answers are defensible; the wrong move is to default to whichever peer's pattern is most recent in context. Q0b makes the user articulate the cognitive answer explicitly.

### Critique of Norman's separation

Jackson's essay https://essenceofsoftware.com/posts/conceptual-models/ argues Norman's separation of "conceptual model" from software is incoherent. The implication for the product-architecture skill: don't treat Norman's "user model" as an artifact that lives in a research deck and informs the build. Treat it as the build itself. The skill's structural output (the handoff brief) should match the user's concept catalog 1:1; everything else is incidental.

## Citation

Jackson, D. (2021). *The Essence of Software: Why Concepts Matter for Great Design.* MIT Press. ISBN 978-0-262-04545-2. Companion site: https://essenceofsoftware.com. Critical essay: https://essenceofsoftware.com/posts/conceptual-models/ (the "Conceptual models missed the point" piece, the cleanest articulation of the cognitive-vs-business inversion).

Adjacent thinking: Indi Young, *Practical Empathy* (Rosenfeld Media, 2015) — surfaces user interior thinking; Donald Schön, *The Reflective Practitioner* (Basic Books, 1983) — naming and framing as a designer's act.
