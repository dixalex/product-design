# Domain taxonomy (v1)

The skill asks the user to name the domain at Intent. Domain gates which reference products are cited in layer options.

## Minimum viable domain list

| Domain | Canonical reference products | Typical primary object |
|---|---|---|
| `crm` | Linear, Close, Attio, Folk, HubSpot | Lead / Contact |
| `productivity` | Linear, Notion, Superhuman, Things, Raycast | Task / Note / Email |
| `dashboard` | Stripe, Vercel, Supabase | Metric / Event / Resource |
| `mobile` | Things, Calendar, Notes, Mail (Apple HIG exemplars) | Item / Record |
| `dev-tool` | VSCode, Cursor, GitHub, Linear | File / PR / Issue |
| `design-tool` | Figma, Framer, Sketch | Canvas / Object / Layer |
| `media` | Spotify, Netflix, YouTube | Track / Episode / Video |
| `feed` | Twitter / X, Reddit | Post / Thread |
| `other` | (user specifies) | (user specifies) |

## How the skill uses this

At the Intent gate, after the JTBD + persona are captured, the skill asks for domain. The taxonomy above drives the dialogue:

1. User picks a domain from the list (or says `other` + names their own).
2. Skill proposes the 2–3 canonical reference products for that domain.
3. User can override ("use Linear, Close, and Raycast") or accept.
4. Those reference products become the `domain_peers` for every subsequent layer's option-proposing.

At each downstream layer (Structure, Flows, Disclosure), when the skill proposes 2–3 options grounded in domain peers, it draws the shapes directly from the `domain_peers` list established here.

## When the reference library ships (v1.1 or later)

Each domain gets a file under `references/patterns-by-domain/<domain>.md` (e.g. `crm.md`). That file documents, for each layer, how the canonical reference products for that domain solve it. The layer-gate dialogue then becomes: "Here are 3 options grounded in your domain peers, sourced from `patterns-by-domain/<your-domain>.md`." Until then, the skill asks the user for their own direct reading of those peers, or falls back to the skill-author's reading of the top two canonical peers.

## Cross-domain innovation

At any layer gate, the user can request a cross-domain surprise. The skill proposes one pattern from a different domain applied to the user's product. Example: "Figma's pages-within-files applied to your CRM — each lead is a 'file', each activity is a 'page'." Higher risk, potentially higher upside. Never the default, always explicit.

Cross-domain surprises are bounded: the skill suggests one per layer at most, and only when the user's peers are reasonably well-fitted (otherwise the surprise is just noise on top of an already-confused option set).

## Fallback: user doesn't know domain peers

At the Intent gate, Q5 ("Which 2–3 products in this domain do you consider reference-quality?") can fail — the user may not have strong reference-products in mind. The fallback:

1. Skill proposes 2–3 canonical peers from this taxonomy for the user's named domain (e.g. for `crm`: Linear, Close, Attio).
2. Skill asks: "OK to anchor on these? (yes / I'll name my own)."
3. If the user picks `yes`, those become `domain_peers` for the rest of the session.
4. If the user picks `I'll name my own`, they supply 2–3 peers inline (standard flow).
5. At any later gate, the user can still override peers ("use Figma instead of Attio for this layer's options").

This fallback is the only exception to the "user is the authority on Intent" rule. It's safe because domain peers aren't Intent-layer *content* — they're reference material used by downstream layers, and the taxonomy's defaults are curated for each domain. The JTBD, persona, and domain itself remain the user's call.

## Domain-of-last-resort: `other`

If no listed domain fits, the user picks `other` and names their own. The skill then asks the user to name 2–3 canonical reference products directly (no taxonomy fallback available). If the user can't name any, the skill surfaces this as a risk: "Without peer references, I can't anchor options at downstream layers. You'll see generic 2–3 options sourced from cross-domain canon. Proceed, or revise to a listed domain?"
