````markdown
# Handoff spec template (visual-design)

The OUTPUT artifact written at spec-write time. Lands at `<project-root>/docs/visual-design/<YYYY-MM-DD>-<slug>.md`. The brief has three audiences:

1. **Human review** — the designer / stakeholder / future-self reads the prose decisions and confirms intent
2. **Git versioning** — the spec is the design record; lives in the project repo
3. **Downstream skill consumption** — `behavior-first-design` reads it as upstream truth (`var(--color-bg)` etc.); `frontend-design` reads it as explicit context the user passes at invocation time

## Shape: Markdown + YAML frontmatter + CSS custom-properties block

```markdown
---
project: "<name>"
date: YYYY-MM-DD
domain: <crm | productivity | dashboard | mobile | dev-tool | design-tool | media | feed | other>     # inherited from structural spec
mood: "<one phrase, 2–6 words>"
type_pairing: "<display + body fonts>"
color_scheme: "<dark-first | light-first | both>; <accent strategy in 1 phrase>"
density: "<compact | comfortable | generous>; <baseline grid>"
motion: "<minimal | expressive>; <duration baseline> ease"
polish: ["<distinctive details>"]
canonical_voices_used: [linear, stripe]
user_voices: []                                    # populated when user supplies references during dialogue
constraints_from_structural_spec: ["<surface — constraint>"]   # see § 2.5 of spec
---

# <Project> — visual spec

## Mood

<1 paragraph: the aesthetic commitment in the user's words. Cite the extraction signals from the structural spec that derived the mood (e.g., "JTBD's emotional verb 'work' + persona 'solo founder, keyboard-first' + scope rejection of 'manager dashboards' → terminal-adjacent, dense, calm").>

## Typography

<Decisions: display font, body font, weights, scale. Voice-personality mapping rationale. Peer precedent cited AFTER derivation. CSS tokens — see Tokens block.>

### Type specimens (SVG; optional but recommended)

```html
<svg width="600" height="180" xmlns="http://www.w3.org/2000/svg">
  <text x="0" y="36"  font-family="Inter Display, system-ui" font-size="32" font-weight="700">Display 32px / 700 — Inter Display</text>
  <text x="0" y="76"  font-family="Inter, system-ui" font-size="20" font-weight="600">Heading 20px / 600 — Inter Variable</text>
  <text x="0" y="108" font-family="Inter, system-ui" font-size="14" font-weight="400">Body 14px / 400 — Inter Variable. Lorem ipsum at the chosen size.</text>
  <text x="0" y="138" font-family="JetBrains Mono, ui-monospace" font-size="12">Mono 12px — JetBrains Mono — chord chips, timestamps, tabular figures</text>
</svg>
```

(Replace font-family chains with the project's actual typefaces. Falls back to system-ui where the project's typeface isn't installed. SVG renders on GitHub / claude.ai / Cowork.)

## Color

<Decisions: scheme (dark/light/both), accent system, palette generation. Semantic-contrast derivation rationale. Accessibility floors (4.5:1 for body; 3:1 for large text; non-text contrast targets). Peer precedent. CSS tokens — see Tokens block.>

### Palette swatches (SVG; optional but recommended)

```html
<svg width="600" height="120" xmlns="http://www.w3.org/2000/svg">
  <rect x="0"   y="0" width="100" height="80" fill="#0a0a0a"/>
  <rect x="100" y="0" width="100" height="80" fill="#1a1f2a"/>
  <rect x="200" y="0" width="100" height="80" fill="#5b8def"/>
  <rect x="300" y="0" width="100" height="80" fill="#f0f4f8"/>
  <text x="50"  y="105" font-family="monospace" font-size="11" text-anchor="middle">--color-bg #0a0a0a</text>
  <text x="150" y="105" font-family="monospace" font-size="11" text-anchor="middle">--color-border #1a1f2a</text>
  <text x="250" y="105" font-family="monospace" font-size="11" text-anchor="middle">--color-accent #5b8def</text>
  <text x="350" y="105" font-family="monospace" font-size="11" text-anchor="middle">--color-fg #f0f4f8</text>
</svg>
```

(Replace hex values with the project's actual palette. Use the `--color-*` token name as the swatch label so CLI readers also see the mapping.)

## Spacing

<Decisions: baseline grid (4pt/8pt), row-height floor, padding scale, density mode. Density-rhythm derivation rationale. CSS tokens — see Tokens block.>

## Motion

<Decisions: duration baselines per surface category (cite `visual-design/references/tokens/motion-defaults.md` if using migrated defaults), easing, what animates vs stays static. Behavior-frequency × mood-energy rationale. Cross-reference to `behavior-first-design/references/motion.md § Frequency-novelty matrix` for fire-policy. CSS tokens.>

## Polish details

### (a) Depth system
<Shadow stack, border weight, focus ring weight + color. Derives from Spacing.>

### (b) Signature details
<Gradient / grain / cursor / distinctive textures. The actual residual after upstream layers commit.>

## Constraints from structural spec

<Per spec § 2.5: surfaces flagged with virtualization, keyboard-primary input, or high-frequency hover-reveals. Each constraint with the visual decision it pinned.>

## User-cited voices

<For each user-supplied voice during the dialogue: name, URL, what about it inspired the user. Pointer to `voices/<slug>.md` (project-local).>

## Tokens (upstream truth — pass explicitly to frontend-design as context)

```css
:root {
  /* Color */
  --color-bg: #...;
  --color-fg: #...;
  --color-accent: #...;

  /* Typography */
  --font-sans: '...', system-ui, sans-serif;
  --font-mono: '...', monospace;
  --text-base: 14px;

  /* Spacing — 8pt grid */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;

  /* Motion — values from visual-design/references/tokens/motion-defaults.md */
  --duration-instant: 0ms;
  --duration-fast: 150ms;
  --duration-medium: 180ms;
  --duration-base: 200ms;
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);

  /* Polish */
  --focus-ring-color: var(--color-accent);
  --focus-ring-width: 2px;
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --shadow-md: 0 4px 12px rgba(0,0,0,0.08);
}
```

## Decisions log

<Chose X over Y because <Mood / canon / voice> — per layer. Each entry one line.>
```
````
