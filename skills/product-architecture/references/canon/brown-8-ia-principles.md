# Brown's 8 Principles of Information Architecture

- **Author/source:** Dan Brown, 2010, "Eight Principles of Information Architecture," *Bulletin of the American Society for Information Science and Technology*, 36(6).
- **Statement:** Eight heuristics that govern how information should be organized across a product — from the design of content-as-objects, through the disclosure of information, to the navigation schemes that let users traverse it, to growth over time.
- **Applies to layer:** Structure (primary); Flows and Disclosure (derived).
- **Why it matters:** The cleanest, most comprehensive framework for information architecture published this century. Every peer design system we surveyed either uses Brown's principles or rediscovers them under different names. Polaris is the only mature design system to cite Brown explicitly. In product-architecture, Brown gives us a canonical question-set that maps 1:1 onto three of our four layers.
- **How the skill uses it:** Structure-layer dialogue treats Brown's principles as the canonical question-set — each principle becomes a decision the skill makes the user articulate. Flows and Disclosure layers invoke specific Brown principles (Front Doors, Focused Navigation, Disclosure, Exemplars) where they fire.

## Full principle(s)

### 1. Principle of Objects
"Treat content as a living, breathing thing with a lifecycle, behaviors and attributes." Every object is a type; types have properties; properties have relationships to other types. A Lead (CRM) has a status, an owner, linked activities, and moves through a pipeline. A Pull Request (dev tool) has reviewers, commits, status checks, and a merge state. Designing the object, not the page, is how you get reusable components, durable URLs, and a product that survives growth.

*Dialogue use:* Structure gate opens by asking the user to enumerate the product's 3–7 primary objects as nouns only. OOUX's noun-extraction technique from the Intent statement supports this — see `canon/ooux-objects-before-actions.md`.

### 2. Principle of Choices
"Create pages that offer meaningful choices to users, keeping the range of choices available focused on a particular task." A page that tries to do ten things does none of them well. A page that offers two well-framed choices feels like a conversation. The principle bounds the design question from above: not "what could go on this page?" but "what is this page *for*?"

*Cross-link:* This is Hick's Law applied at the IA scale — see `behavior-first-design/references/foundations.md § Hick's Law`. Dialogue use: Structure gate asks, per surface, "what is this surface for" and rejects answers with more than one primary purpose.

### 3. Principle of Disclosure
"Show only enough information to help people understand what kinds of information they'll find as they dig deeper." Disclosure at IA scale is not "click to expand" — it's the ladder of signposts that lets a user know there is more below the fold, behind that link, inside that drawer. Sparse enough to scan; rich enough to promise.

*Dialogue use:* Disclosure gate (our fourth layer) asks the user to classify each surface's information into always-visible / one-click-away / two-plus-clicks-deep. See `layers/disclosure.md`.

### 4. Principle of Exemplars
"Describe the contents of categories by showing examples of the contents." A category labeled "Resources" with three example titles underneath is infinitely more legible than the label alone. In CRM UI: a pipeline stage labeled "Qualified" with two sample leads shown as chips. In Shopify: a product category with three thumbnails. Exemplars do the disambiguation that labels alone can't.

*Dialogue use:* Structure + Disclosure cross-cutting. When the skill proposes view options, it proposes the exemplar form too ("list with 2–3 sample leads preview-visible per stage").

### 5. Principle of Front Doors
"Assume at least half of the website's visitors will come through some page other than the home page." Every surface is a legitimate entry point. A deep-linked URL, a shared link, a cmd-K jump, a notification click — each of these arrives a user at a non-home surface. That surface must orient them: name the object they're looking at, show them where they are, offer one obvious next move.

*Dialogue use:* Flows gate asks, per primary surface, "what must be true for a cold-arrival user to orient themselves?" — see `layers/flows.md`. In Linear: every issue URL includes breadcrumbs (Project → Cycle → Issue) and the sidebar is always populated. In Close: every lead URL includes the pipeline the lead belongs to.

### 6. Principle of Multiple Classification
"Offer users several different classification schemes to browse the site's content." Users' mental models differ — some think in folders, some in tags, some in saved queries, some in people. A good product supports all of them over the same underlying objects. Not "pick one," but "same data, multiple lenses." This is why Linear supports both Projects (containers) and Labels (tags) and Cycles (time) and Views (saved queries) over the same issues.

*Dialogue use:* Structure gate asks about the axes on which users will want to re-slice objects — typically surfaces as tabs, statuses, owners, date ranges, saved views.

### 7. Principle of Focused Navigation
"Don't mix apples and oranges in your navigation scheme." A sidebar that mixes objects (Leads, Pipelines) with actions (Import, Export) with utility (Help, Settings) at the same level confuses users about what "clicking a sidebar item" means. Keep each nav scheme focused on one kind of thing: object navigation, task navigation, utility navigation — each gets its own region.

*Dialogue use:* Flows gate asks the user to sort proposed nav items into "object navigation / task navigation / utility navigation" and rejects mixed-scheme sidebars. In Close: left sidebar is object (inbox, pipelines); top-right is utility (account, help). In Linear: left sidebar is object (Inbox, My Issues, Projects); cmd-K handles actions.

### 8. Principle of Growth
"Assume the content you have today is a small fraction of the content you will have tomorrow." The IA that handles your first 50 leads must also handle your 50,000th. Flat lists break at scale; category trees rigidify; unbounded tags become a second taxonomy. Plan for growth by leaving an axis of the IA open (tags, saved views, user-defined filters) rather than hardcoding every classification.

*Dialogue use:* Structure gate asks, "what does this IA look like at 100× the data? Which axes stretch and which break?" This is also where the skill cross-links to Polaris's "plan for growth" foundation — see `canon/polaris-ia-foundations.md`.

## Citation

Brown, D. (2010). "Eight Principles of Information Architecture." *Bulletin of the American Society for Information Science and Technology*, 36(6), 30–34. Available via the `principles.design` collection: https://principles.design/examples/eight-principles-of-information-architecture (retrieved 2026-04-21; verify at skill-ship time). Local cached scrape: `pine-research/.firecrawl/scratch/product-arch/brown-8-ia.md`.
