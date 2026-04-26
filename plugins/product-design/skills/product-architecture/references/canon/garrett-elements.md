# Garrett — The Elements of User Experience

- **Author/source:** Jesse James Garrett, *The Elements of User Experience: User-Centered Design for the Web and Beyond*. 1st ed. 2002, New Riders; 2nd ed. 2010, New Riders. ISBN 978-0-321-68368-7.
- **Statement:** User experience can be decomposed into five dependent planes — Strategy, Scope, Structure, Skeleton, Surface — where each higher plane's choices are constrained by the plane below. The 5-plane model is the canonical scaffold for organizing product-design concerns.
- **Applies to layer:** Cross-cutting. Garrett is the vocabulary that the product-architecture layer names map onto.
- **Why it matters:** Garrett's 5-plane model is the most widely-cited organizing framework in modern UX. Every peer design system implicitly reproduces some subset of it. Naming the mapping explicitly — where our "Intent" maps onto Strategy+Scope, where our "Structure" maps onto Structure, where `behavior-first-design` picks up Skeleton, where `visual-design` picks up Surface — lets the skill speak canon fluently.
- **How the skill uses it:** Garrett is not invoked directly at any one gate. He's the translator. When a user (or a future agent) arrives with Garrett vocabulary ("we haven't done Strategy yet"), the skill can recognize it and route to Intent.

## Full principle(s)

### The Five Planes

Garrett's Chapter 2, "Meet the Elements," lays out the five planes from bottom (most abstract) to top (most concrete):

- **Strategy.** What the product is for — whose needs it meets, what business objectives it satisfies. The Strategy plane is "what people running the site want to get out of it" crossed with "what users want to get out of the site."
- **Scope.** Which features and functions are in, which are out. A plane of product-level constraint: not "how it works" but "what it is."
- **Structure.** How the Scope-plane features fit together. Where users can go from a given page; which categories the content is organized into; how the user gets from A to B.
- **Skeleton.** The concrete arrangement of interface elements on a page — which button, which tab, which text block goes where. Optimized for "maximum effect and efficiency."
- **Surface.** The final visible layer — typography, color, imagery, icons. What the user actually sees and clicks.

### Ripple dependency

Garrett's central claim: "Each plane is dependent on the planes below it. So, the surface depends on the skeleton, which depends on the structure, which depends on the scope, which depends on the strategy." Design decisions made on a lower plane constrain — and therefore ripple up into — every higher plane. A change to Strategy forces rethinking Scope, which forces rethinking Structure, and so on. A change at the Surface plane rarely forces anything below.

Garrett is careful: this does NOT mean work on each plane must finish before the next begins. Instead: "work on any plane cannot finish before work on lower planes has finished." Lower planes must be settled first; higher planes must not lock in before lower ones do.

### Basic duality: functional vs. information-oriented

Garrett splits the middle planes by whether the site is "software" (functional) or "hypertext" (information-oriented). Most modern SaaS products are hybrid: they present information (feeds, lists, dashboards) AND support task completion (create, edit, approve, submit). This split is why many of Brown's principles apply regardless: Brown's 8 principles hold for both sides of Garrett's duality.

## Mapping onto product-architecture's four layers

| Garrett plane | Product-architecture skill | Notes |
|---|---|---|
| Strategy | **Intent** (ours) | We use "Intent" because it reads better in agent prompts and matches JTBD vocabulary. |
| Scope | **Intent** (ours, scope-down component) | We fold Scope into Intent via Saarinen's "scope is a feature" and Shape Up's "appetite." |
| Structure | **Structure** (ours) | 1:1 match. |
| Skeleton | **Flows** (ours) + `behavior-first-design` | Flows owns the wayfinding/navigation half of Skeleton. Behavior-first owns the within-task interaction half. |
| Skeleton (disclosure half) | **Disclosure** (ours) | What information surfaces at what visibility tier. |
| Surface | `visual-design` (sibling skill in this plugin) | Mood, Typography, Color, Spacing, Motion, Polish — visual brief at `<project-root>/docs/visual-design/<date>-<slug>.md`. |

### Where Garrett stops short

Garrett names the planes and describes the dependencies but does not prescribe *method*. He tells you Structure depends on Scope, but doesn't say *how* you derive Structure from Scope. The product-architecture skill fills this gap by adopting OOUX's noun-extraction technique for the Intent → Structure bridge, and Brown's 8 principles for the Structure → Flows → Disclosure ladder. See `canon/ooux-objects-before-actions.md` and `canon/brown-8-ia-principles.md`.

### Why the 5-plane model still holds in 2026

Recent critiques of Garrett's model often argue it's too waterfall for modern iterative practice. Garrett himself addressed this in the 2010 2nd edition — work on planes is concurrent, not sequential. The model describes *dependency*, not *sequence*. Every major design system that shipped after 2010 (Polaris, Material, HIG, Atlassian, Fluent) preserves the dependency structure even where it doesn't use Garrett's vocabulary. This is why we invest in one canonical vocabulary-translator file rather than replacing the model.

## Citation

Garrett, J. J. (2010). *The Elements of User Experience: User-Centered Design for the Web and Beyond* (2nd ed.). Berkeley, CA: New Riders. ISBN 978-0-321-68368-7. Chapter 2 ("Meet the Elements") is the canonical source for the 5-plane model. Online PDF of the original chapter: http://www.jjg.net/elements/pdf/elements_ch02.pdf. Local cached scrape: `pine-research/.firecrawl/scratch/product-arch/garrett-ch02.md`.
