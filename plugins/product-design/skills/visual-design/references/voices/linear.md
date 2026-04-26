# Linear — issue-tracking / project-management product

- **Source:** linear.app, linear.app/method, linear.app/blog/our-redesign
- **Captured:** 2026-04-26
- **Layer relevance:** Mood, Color, Spacing, Polish

## Aesthetic profile

Linear shows more information per screen than any direct competitor — issue rows, metadata columns, status chips, cycle indicators — yet the UI reads quieter than tools with half the density. The mechanism is chroma-tinted neutrals rather than pure gray: every background and border carries a faint cool-blue cast that keeps the surface feeling alive without introducing hue contrast. Decorative noise is near zero. No gradients in the product UI, no drop shadows except at modal and popover boundaries where lift must communicate layering. The result is a UI that signals "this is a serious instrument" without signaling "look at me."

The entire color system converges on a single accent hue — an electric blue-purple — which appears exclusively at focus rings, primary CTAs, active state indicators, and brand elements. Everywhere else is neutral. That scarcity earns the accent legibility: when it appears, it means something. Hover, active, and disabled states for interactive elements are derivations of the same hue at shifted lightness levels — no secondary palette, no semantic reds or greens in the chrome layer (only in status chips where domain meaning requires them). Dark-mode is the canonical experience; light mode exists and is well-executed, but reads as the alternate.

Typography is Inter Display for headings and Inter Variable for body, a choice that reflects restraint over expressiveness: humanist sans, high readability at small sizes, no personality claim. Icons appear only where text would be ambiguous or wasteful; most affordances are text-labeled. This is a tool that respects the user's time over the designer's portfolio.

## Per-layer treatment

### Mood
- Decision: calm + dense — information-rich, visually quiet, no decorative flourish.
- Why it works: Linear's users are software engineers and product managers spending 6+ hours daily in the tool. Loud aesthetics accumulate fatigue; calm density lets the user's own data carry the foreground.
- When NOT to copy: marketing sites, content surfaces (blogs, docs), consumer products, onboarding flows. Linear's restraint signals "professional instrument" — it reads cold in contexts where warmth or narrative are load-bearing.

### Color
- Decision: single accent hue (electric blue-purple), near-black backgrounds (`#0a0a0a`–`#111111`), chroma-tinted cool neutrals (not pure gray), semantic colors only inside domain-meaningful chips.
- Why it works: accent scarcity means the user always knows when something is interactive or selected; chroma-tinted neutrals prevent the UI from reading as dead ash while preserving near-zero decorative noise.
- When NOT to copy: products that need multiple semantic colors simultaneously visible at the chrome level — category tags, urgency flags, multi-pipeline status. Linear's single-accent discipline actively hides this information; a tool that must surface it needs a richer palette.

### Spacing
- Decision: compact density — list row heights 28–32px, 8pt base grid, padding at minimums consistent with touch-target floors, no ornamental whitespace between list items.
- Why it works: professional users need screen surface for data, not breathing room. The 8pt grid keeps alignment consistent across density levels without requiring explicit spacers.
- When NOT to copy: read-mostly content surfaces (documentation, editorial), mobile-primary experiences (touch targets demand 44pt minimums), onboarding or low-frequency flows where cognitive load from density is disproportionate to frequency of use.

### Polish
- Decision: 1px hairline borders delineate structure; focus rings are 2px in accent color; box shadows appear only at dialog/popover layer to communicate elevation boundary; no decorative shadows anywhere else.
- Why it works: hairlines are skeletal — they mark zones without adding visual weight. Focus rings command attention precisely because the surrounding UI is visually quiet; a 2px accent ring reads loudly against neutral backgrounds. Elevation shadows only where elevation is semantically true.
- When NOT to copy: touch-primary surfaces where keyboard focus discipline adds no value; brand-expressive products where elevation and shadow are part of the aesthetic language rather than functional signals.

## Citation

- linear.app (product homepage)
- linear.app/method (Linear Method — design philosophy documentation)
- linear.app/blog/our-redesign (2023 redesign post)
