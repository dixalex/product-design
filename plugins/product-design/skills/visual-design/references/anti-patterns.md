# Anti-patterns — visual-design (decision layer)

Generic-AI aesthetic patterns to avoid at decision time. Visual-design covers *decision* anti-patterns; `frontend-design` covers *execution* anti-patterns. When the two conflict, visual-design wins because it's upstream — the spec commits an aesthetic; frontend-design implements within it.

## Typography anti-patterns

- **Inter / Roboto / Geist / Söhne on every project.** These fonts are reasonable defaults but signal "AI-generated" when used reflexively. Pick a font that carries the chosen Mood; don't default to the most common one.
- **Generic system-font-only fallback.** `font-family: -apple-system, BlinkMacSystemFont, sans-serif` without a chosen display face = no aesthetic commitment.
- **Monospace + sans pairing without purpose.** Monospace for chord chips, timestamps, numerical alignment is fine; monospace for everything is "I want to look technical" not a real choice.
- **No type scale.** Using arbitrary px sizes (13px, 15px, 17px) instead of a deliberate scale produces visual chaos.

## Color anti-patterns

- **Purple gradients on white.** The most-used AI-tell. Includes "indigo-500 to purple-600" Tailwind defaults.
- **Default Tailwind palette unmodified.** `text-gray-500` / `bg-blue-500` straight out of the box reads as unstyled.
- **Single-accent without contrast hierarchy.** One accent everywhere (every button, every link, every focus ring) flattens hierarchy.
- **Light + dark with no commitment.** Shipping both modes "to be safe" without picking the load-bearing one results in neither feeling primary. Pick the primary mode; the other is the alternate.

## Spacing anti-patterns

- **Padding by feel.** `p-4` here, `p-6` there, no scale. Pick a baseline grid (4pt or 8pt) and stick to it.
- **Generous spacing on dense data.** A spreadsheet-like surface with 24px row padding wastes the user's screen.
- **Compact spacing on read-mostly content.** A blog post at 14px line-height-1.3 fights legibility.

## Motion anti-patterns

- **Spring physics in product UI.** Bounce is consumer-app vocabulary; product UI uses ease-out / ease-in.
- **Animating frequent actions.** Per `behavior-first-design/references/motion.md` frequency-novelty matrix — keyboard nav, row selection, hover reveals all 0ms. Animating these creates fatigue.
- **Page-load stagger reveals on every navigation.** Once is delight; on every route change is friction.
- **Skipping `prefers-reduced-motion`.** Non-negotiable accessibility constraint.

## Polish anti-patterns

- **Drop shadow stack from a Sketch tutorial.** "soft / medium / hard" shadow without thinking about depth meaning.
- **Glass / blur on everything.** Used reflexively, not for spatial layering.
- **Custom cursors that don't load.** A custom cursor that's invisible on first paint reads as broken.
- **Decorative noise/grain at low resolution.** Visible banding signals "I tried."

## What's NOT an anti-pattern

- **Following a single peer's aesthetic deliberately.** "Make it feel like Linear" is a real commitment if you mean it. The anti-pattern is *defaulting* to Linear because it's recent in context.
- **Conventional choices that earned their convention.** Black-on-white body text is fine. The anti-pattern is when convention is unexamined, not when it's chosen.

## Cross-reference

Frontend-design's anti-patterns: see `frontend-design/SKILL.md` (Anthropic official). Their list covers execution-time defaults the skill is told to avoid. This file covers decision-time defaults the visual spec is told to commit against.
