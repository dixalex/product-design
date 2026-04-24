# OOUX — Objects Before Actions

- **Author/source:** Sophia V Prater, "Object-Oriented UX," *A List Apart*, 2015. Ongoing framework at https://www.ooux.com.
- **Statement:** Design the objects (nouns) of a system and their relationships before designing the actions (verbs) that operate on them. "The content is the navigation."
- **Applies to layer:** Structure (primary); bridges Intent → Structure via noun-extraction from the JTBD statement.
- **Why it matters:** OOUX is the missing method between Garrett's Strategy and Structure planes. Garrett says Structure depends on Strategy but doesn't specify how to derive one from the other. OOUX does: extract the nouns from the strategy, those are your candidate objects. This turns a vague Intent statement into a concrete, scaffolded object model in a single move.
- **How the skill uses it:** Structure gate runs a lightweight noun-extraction on the user's Intent statement to seed the object list, then asks the user to confirm/edit. The first Structure question ("list the 3–7 primary objects") is OOUX verbatim.

## Full principle(s)

### Objects before actions

In a conventional design process, designers jump from strategy to wireframes — they sketch screens and flows, worrying about buttons and layouts before they've defined *what the software is about*. OOUX reverses this. Prater's central move: before you wireframe anything, you identify the real-world objects in your users' mental model, define their attributes, and map their relationships. Only then do you decide which actions each object supports.

In Prater's own illustration (a cooking site): *recipes*, *chefs*, and *ingredients* are the objects. A recipe has a chef and a list of ingredients. A chef has many recipes. An ingredient appears in many recipes. Once the object graph is clear, navigation becomes obvious: from a recipe, you can reach its chef and its ingredients; from an ingredient, you can reach every recipe that uses it. The content is the navigation.

### Noun-extraction technique

Prater's process begins with the project brief (the JTBD equivalent) and literally highlights the nouns. Some become objects; some are attributes of objects; some are collections (list-views in disguise). The technique is first-grade simple but requires practiced judgement:

- **Promote recurring nouns to objects.** If *challenge* and *solution* keep appearing, they are core objects.
- **Demote abstract nouns.** *Exposure* is an outcome, not a system object.
- **Unmask disguised collections.** *Library*, *catalog*, *map*, *calendar* are usually filterable views of a core object (solutions, products, locations, events) — not objects in their own right.
- **Infer objects from verbs.** "Commenting on" implies a *comment* object even if no one said so outright. "Following up" implies a *follow-up* object.
- **Note states.** An object with multiple states (posted, in-progress, closed) is a hint the object has a lifecycle worth modeling.

### Why objects first produces better IA

Prater's argument, summarized from the *A List Apart* essay:

1. **Match the user's mental model.** Users think in real-world objects (a contact, an email, a deal), not in design primitives (a form, a modal). Designing object-first respects this.
2. **Reduce accidental complexity.** Once the object model is stable, the wireframes stop mutating every sprint. Engineers reverse-engineer your designs for objects anyway — give them the model up front.
3. **Grow and maintain cleanly.** New features add new objects or new attributes, not new pages. Objects can be iterated without tearing down the rest of the system.
4. **Better APIs "for free."** A domain modeled as objects with clear relationships exports as a REST or GraphQL surface with no additional design work.
5. **Contextual navigation as the default.** "Get to content through content" — from a recipe, jump to the chef or the ingredient. The user never hits a dead end. Persistent top nav becomes the fire escape, not the main path.

### The OOUX process (five-stage)

Prater's later work on ooux.com formalizes the method as a five-stage process — it is longer than the skill uses in-session but informs the Structure-gate's working principle set:

1. **Discover objects.** Noun-extraction from the brief + user research.
2. **Define objects.** Give each object a canonical name, core metadata, primary states.
3. **Relate objects.** Draw the lines: which objects contain which? Peers? Dependents?
4. **Rank objects.** Which is the primary object the user starts with? Which are supporting?
5. **Design CTAs per object.** Only now do actions enter the picture — per object, what verbs apply?

The product-architecture skill collapses stages 1–4 into its Structure-gate dialogue and defers stage 5 to `behavior-first-design` (actions become the triggers for components).

### Relationship to Dan Brown's principles

OOUX operationalizes Brown's Principle of Objects. Where Brown names the principle ("treat content as a living thing"), OOUX ships the method ("here's how to find the objects in your brief"). The two are complementary: the skill cites Brown as canonical, OOUX as procedural.

## Citation

Prater, S. V. (2015). "Object-Oriented UX." *A List Apart*, issue 432. https://alistapart.com/article/object-oriented-ux/ (retrieved 2026-04-21). Ongoing framework and process documentation: https://www.ooux.com. Local cached scrape: `pine-research/.firecrawl/scratch/product-arch/ooux-alistapart.md` and `ooux-home.md`.

Original influence: Collins, D. (1995). *Designing Object-Oriented User Interfaces.* Addison-Wesley. Cited by Prater as the earlier-term-of-art source for OO thinking in UI.
