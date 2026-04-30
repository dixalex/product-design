# Ousterhout — A Philosophy of Software Design

- **Author/source:** John Ousterhout, *A Philosophy of Software Design* (Yaknyam Press, 2018; 2nd ed. 2021). https://web.stanford.edu/~ouster/cgi-bin/aposd.php
- **Statement:** Complexity is the fundamental challenge in software design. Good design produces *deep modules* — large internal scope hidden behind small interfaces — and rejects *shallow modules* that leak complexity through wide APIs.
- **Applies to layer:** Structure (primary, especially Round-3 collapse step); cross-cutting at Disclosure (Tesler trade-off framing).
- **Why it matters:** Ousterhout names the pattern that distinguishes well-architected software from feature-soup: not the count of modules, but the *depth* of each. A product with a few deep modules is easier to reason about, change, and extend than a product with many shallow modules. For an IA / object model, this maps directly to OOUX's Round-3 collapse — the choice to Combine / Downgrade / Offload / Eliminate is, underneath, a deep-module decision. Ousterhout gives the dialogue a named principle to invoke when arguing collapse.
- **How the skill uses it:** Cited at Structure layer's Round-3 collapse step (after OOUX object derivation). Each candidate object asks: does this earn its API surface, or fold into a deeper neighbor? Ousterhout's depth ratio (functionality hidden ÷ interface area exposed) gives Round-3 dialogue a named principle rather than a gut call.

## Full principle(s)

### Deep modules vs shallow modules

The book's load-bearing claim: a module's value is its depth — functionality hidden inside divided by interface surface area exposed. Deep modules hide a lot behind a small API; shallow modules expose nearly everything they do. Shallow modules are easy to write and seem harmless in isolation, but they compound complexity at the system level. Every consumer must understand the full surface area, and that cost multiplies with every caller.

For an IA / object model, the mapping is direct. A "Lead" object with 30 fields, 12 verbs, and 8 sub-types is shallow: its full internal structure is exposed as the API. The right response is collapse — which fields are derivable? Which verbs reduce via Combine? Which sub-types are really one concept with a state attribute, collapsible via Downgrade? The result is a deeper Lead: same internal richness, smaller exposed surface.

This is the principle behind OOUX Round 3 (Combine / Downgrade / Offload / Eliminate). Ousterhout names it; OOUX operationalizes it. Before Round 3, the object list is a feature inventory. After Round 3, it is a module design. The discipline is the same: refuse to let the implementation leak through the interface.

### Information hiding (the API surface area question)

Ousterhout's corollary to deep modules is information hiding as design discipline. Every object, every layer, every spec section carries the same question: what does the consumer need to know versus what is implementation detail? A deep spec hides the deliberation that produced its decisions; a shallow spec leaks implementation choices into the consumer's mental model and forces the consumer to re-derive context the author already worked through.

The pattern appears at every layer of the plugin. Tokens are the deep-module pattern made concrete: consumers see `var(--color-bg)`; they do not see the HSL derivation, contrast verification, or rejection of three alternatives. The token is the API; the rest is hidden. The behavior-first-design component library applies the same logic — a component spec hides the interaction edge-cases inside a named contract; callers do not need to know which edge-cases were considered.

The plugin's pivot from code-emitter to spec-emitter (v0.4.0) is information hiding at the process level. The spec is the API; the dialogue that produced it is hidden. Consumers of the spec — developers, future design iterations, handoff reviewers — work against a stable surface, not against the raw transcript of decisions.

### Tactical vs strategic programming

Ousterhout's most practically useful framing: tactical programmers chase features one at a time, accumulating shallow modules as a side effect. Strategic programmers invest in design — they accept short-term friction to reduce long-term complexity. The distinction is not about effort; it is about where the investment lands.

The parallel for IA design: tactical IA produces feature-list architectures where every surface justifies its existence by what it adds. Strategic IA produces deep object models where every surface justifies its existence by what it lets the user abstract away. Round-3 collapse is the strategic discipline applied to OOUX output — refusing to ship the shallow version of the model where every Round-1 candidate survives intact. The plugin's spec-first approach is the same investment: the spec is the durable artifact; code is downstream tactical execution.

### Complexity is incremental and viral

Ousterhout's most uncomfortable claim: complexity accumulates through individually defensible decisions. A small unjustified addition to an API surface seems harmless; ten of them compound into a shallow module. The diagnosis is hard precisely because each step looked reasonable. The discipline: evaluate each addition against the depth ratio — does this earn its surface area, or does it leak implementation?

For specs, the same dynamic applies. Every additional frontmatter key or layer subsection has to pay back. The skill's word caps — SKILL.md ≤500 lines; layer files ≤2,000 words — are enforcement mechanisms. Caps make the cost of additions visible; without them, the spec grows shallow through accumulated "one more thing" additions that each seemed justified in isolation.

The v0.4.0 audit's "Tesler trade-off" framing named the same dynamic from the opposite direction: ~500–800 lines of net deletion was a feature, not a bug. The audit was a Round-3 collapse applied to the skill itself — Combine, Downgrade, Offload, Eliminate, until what remained was deep.

## Citation

Ousterhout, J. (2018; 2nd ed. 2021). *A Philosophy of Software Design* (2nd ed.). Yaknyam Press. ISBN 978-1-7321022-0-0. https://web.stanford.edu/~ouster/cgi-bin/aposd.php

Adjacent thinking: Kent Beck, *Extreme Programming Explained* (Addison-Wesley, 2000) — "invest in design every day" as the strategic-programming discipline made operational. David Parnas, "On the Criteria to Be Used in Decomposing Systems into Modules" (1972) — the original information-hiding paper that Ousterhout explicitly builds on; Parnas named the principle, Ousterhout quantified it.
