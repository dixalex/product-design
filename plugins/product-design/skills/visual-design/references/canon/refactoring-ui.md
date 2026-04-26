# Refactoring UI

- **Author/source:** Adam Wathan & Steve Schoger, *Refactoring UI*, self-published, 2018. https://refactoringui.com
- **Statement:** A practical guide to making frontend interfaces look intentional without a formal design background. Not a design system — a checklist of operational moves across typography hierarchy, color depth, spacing rhythm, and polish details that compound into a professional-looking result.
- **Applies to layer:** Mood, Typography, Color, Spacing, Polish (workhorse — fires at most layers).
- **Why it matters:** It is the single most operationally-useful reference for a developer-turned-designer or an AI generating frontend code. The advice is concrete and testable: not "use good typography" but "size and weight define hierarchy, not color." Not "choose color carefully" but "every brand color is a 10-step lightness scale, not a flat hex." Because the principles are explicit and checkable, they survive handoff from dialogue to implementation without losing signal. Every working frontend designer has internalized at least half of this book; the canon entry makes the implicit explicit so the skill can invoke it by name.
- **How the skill uses it:** Cited at five layers. Typography: hierarchy via size + weight, not color (`§ Hierarchy via size + weight`). Color: lightness scale generation — the skill proposes a 50→900 scale, not a single accent hex (`§ Lightness scales not flat hues`). Spacing: 8pt grid baseline as default derivation (`§ Spacing is harder than color`). Mood: chroma-direction in neutral palette (`§ Don't use grey when you mean color-with-low-saturation`). Polish: intentional shadow stack with named elevation levels (`§ Depth requires intent`).

## Full principle(s)

### 1. Hierarchy via size + weight, not color

The thesis: when designers reach for color to signal importance — making a heading blue, a label green, a call-to-action orange — they reduce hierarchy's resolution. Size and weight carry native ordinal meaning that humans read pre-consciously; color does not. The operational move: establish the heading's rank through scale (larger) and weight (heavier), and set its color to the same near-black as body text. Reserve color for semantic roles — error, success, brand accent — not rank. A refined dark-mode interface demonstrates this most clearly: it uses three or four shades of near-white at different opacities to signal hierarchy, with a single accent color doing semantic work. Color is the accent layer. Hierarchy is the size-and-weight layer. Conflating them degrades both.

### 2. Lightness scales not flat hues

Refactoring UI's most-cited thesis and the one most consequential for code generation. A "brand color" is not `#1976d2` — it is a scale: `--color-50` (background tint, near-white), `--color-100` through `--color-400` (light states), `--color-500` (mid / default), `--color-600` through `--color-800` (dark emphasis), `--color-900` (deep, near-black). Without the scale, every depth variation — hover, active, disabled, inverse — must be invented at use-time, producing inconsistency across components. With the scale, those states become derivable: hover is `--color-600`, disabled is `--color-200`, background tint is `--color-50`. The recommended color space for generating scales is HSL or OKLCH (OKLCH preserves perceptual uniformity across hues better). Tools that apply this: Tailwind CSS's palette, Radix Colors, the Refactoring UI Companion. The skill defaults to generating a 9-step OKLCH scale at Color layer.

### 3. Spacing is harder than color

The counter-intuitive claim: spacing is the highest-leverage decision in interface design and the one most designers underinvest in. The 8pt grid argument: pick 8 as the base unit (or 4 for finer granularity) and never deviate from multiples — 4, 8, 12, 16, 24, 32, 48, 64. Arbitrary values like 13px or 22px are the tell; they produce interfaces that look almost-right rather than considered. Padding is generally larger than margin for interactive elements because padding hits the interaction target and margin collapses. Vertical rhythm matters: line-height multiples should sync with the spacing scale so body text and container padding feel related, not coincidental. The book's practical note: when a layout feels "off" and color is fine and type is fine, the spacing is wrong.

### 4. Don't use grey when you mean color-with-low-saturation

Pure `rgb(128,128,128)` gray reads as flat, institutional, hospital-corridor. An interface that feels alive — even a minimal, neutral one — uses chroma-tinted neutrals: warm grays (slight red-orange bias), cool grays (slight blue bias), or slate (blue-leaning with moderate chroma). The operational fix: pick a chroma direction at the Mood layer — does the product lean warm or cool? — and bake that direction into every "neutral" shade. Warm interfaces feel approachable; cool interfaces feel precise. The choice is a Mood-layer decision that ripples into every background, border, and surface color. The book's framing: there is no such thing as a truly neutral gray in a designed interface; the question is whether the chroma direction is intentional or accidental.

### 5. Depth requires intent

A shadow stack with named levels — `--shadow-sm`, `--shadow-md`, `--shadow-lg`, `--shadow-xl` — is load-bearing only when each level corresponds to an elevation role: sm = card lift (resting surface above background), md = popover or floating action, lg = modal or sidebar overlay, xl = full-screen takeover. Shadows without an elevation model are decoration. The book's corollary for dark mode: against a dark background, darker shadows vanish because elevated surfaces cannot shadow below their containing darkness. The fix is to use background-lightness lift instead — a card on a dark surface is lighter than the background, not shadowed — or to use subtle inset borders. Shadow and elevation are the same concept; the implementation differs by background lightness.

### 6. Stealing convincingly

The book's framing for peer-design reference: look at existing interfaces as precedent, not source. When to look: after derivation, to validate that your choices land in a recognizable register. When not to look: when you are stuck and want to fill a gap by copying — that is peer-mimicry, not precedent use. The distinction matters because copying a peer interface imports its constraints, its brand decisions, and its product context, which may not match yours. The operational rule: derive first (from Mood, JTBD, persona), then scan peers to confirm your derivation is legible. If a peer's choice validates yours, cite it as convention precedent (Jakob's Law). If it contradicts yours, that is a signal to re-examine the derivation, not to copy the peer. The skill applies this as the options pattern: propose derivation-based options first, cite canonical voices as precedent second.

## Citation

Wathan, A. & Schoger, S. (2018). *Refactoring UI*. Self-published. https://refactoringui.com. ISBN: none (digital book, no print edition).
