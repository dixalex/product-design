# Apple HIG Foundations

- **Author/source:** Apple Inc., *Human Interface Guidelines — Foundations*. https://developer.apple.com/design/human-interface-guidelines/foundations (living document; updated per OS release).
- **Statement:** Apple's foundational design guidance covering color, typography, spacing, layout, motion, materials, and accessibility across iOS, macOS, watchOS, and visionOS. Not a design theory text — a working specification from the highest-volume consumer software platform on earth, continuously revised under real-world production pressure.
- **Applies to layer:** Color (semantic color systems, contrast accessibility floors), Spacing (layout density, hit-target minimums), Motion (duration, easing, reduced-motion compliance).
- **Why it matters:** The most-iterated public foundations from a design system at scale. Cross-platform consistencies are the most-defended decisions in software UI — survived billions of devices, multiple screen technologies, and repeated accessibility audits. HIG foundations are a sane default not because they are definitive, but because they have been pressure-tested at a scale no studio can replicate.
- **How the skill uses it:** Color layer cites HIG for contrast floors (4.5:1 body, 3:1 large text/non-text) and the semantic-color model (`label`, `system-background`, `accent-color`) that adapts to dark mode without brittle hex binds. Spacing layer cites HIG for hit-target minimums — 44×44 pt on iOS touch, 24×24 px on keyboard-only web — as density floors compact-layout decisions cannot go below. Motion layer cites HIG for "make motion meaningful" and `prefers-reduced-motion` compliance.

## Full principle(s)

### 1. Color — semantic vs literal; system colors that adapt

HIG distinguishes literal colors (a fixed hex value) from semantic system colors that resolve differently per context. Semantic colors — `label`, `secondary-label`, `system-background`, `accent-color` — adapt automatically to dark mode, high-contrast mode, and Increase Contrast accessibility settings. Software UI should bind to semantic names; literal hex is reserved for brand values that must not adapt, and even then paired with dark-mode alternates. Contrast floors: 4.5:1 for body text, 3:1 for large text (18pt+ regular or 14pt+ bold) and non-text UI elements (WCAG AA). Vibrancy surfaces add a 4-step opacity hierarchy — `primary`, `secondary`, `tertiary`, `quaternary` — for layering on translucent backgrounds. Literal hex is a deliberate override, not the starting point.

### 2. Typography — system text styles and Dynamic Type

HIG names type roles, not sizes: Title 1–3, Headline, Body, Callout, Footnote, Caption 1–2. The role is what the layout commits to; the point value is the default rendering for the user's font-size preference. Layouts must reflow when the user scales via Dynamic Type — truncating at large sizes fails accessibility review. SF Pro and SF Mono are calibrated for Apple display rendering; tabular figures (monospaced numerals) handle numeric columns where digit shifting would distract. Operational discipline: declare by role, wire to Dynamic Type, test at xSmall and xxxLarge Accessibility extremes. Never spec a raw point size without a named role.

### 3. Layout — safe areas, standard margins, and hit-target minimums

Layouts must respect safe-area insets — system-reserved zones around notches, Dynamic Island, home indicators, and status bars. Content bleeding into unsafe zones is clipped at runtime. Standard margins vary by device class; adaptive layouts switch between them via size class. The critical floor: minimum interactive hit target is 44×44 pt on iOS touch surfaces; App Store review can reject below this. On keyboard-navigable web the floor is 24×24 px for focus targets. Both are hard floors for the Spacing layer — density commitments that push below them must be escalated, not overridden.

### 4. Motion — meaningful motion and reduced-motion compliance

HIG grounds animation in one principle: motion communicates state change, not decoration. A sheet slides up because it enters from below the active layer; a navigation push moves left because it goes deeper in a hierarchy; an alert fades because it has no directional origin. Standard easing: `ease-in-out` for most interactions; `ease-out` for entering elements. Avoid `linear` in UI (reserve for loaders). Spring physics: HIG is permissive — native controls use springs for playful feel. This conflicts with behavior-first-design's frequency-novelty matrix, which overrides toward restraint on productivity surfaces. `prefers-reduced-motion` compliance (durations near-zero, slides replaced by crossfades) is non-negotiable regardless; spring permissiveness is context-gated by tone register.

### 5. Materials — depth via translucency, not shadow stacks

Translucent materials (`ultraThin`, `thin`, `regular`, `thick`, `ultraThick`) create depth by letting background context show through at varying blur levels. Hierarchy is expressed through material thickness, not shadow stacking. Shadow is reserved for opaque surfaces that float above their background. On dark surfaces, shadows vanish (you cannot shadow below containing darkness); the fix is background-lightness lift — the elevated surface is lighter than its container. Practical rule: reach for material tiers first when surfaces overlap; reach for `--shadow` only when the surface is opaque and the elevation relationship is unambiguous.

### 6. Accessibility — five axes the visual spec must commit on

HIG organizes accessibility into five domains mapped to the skill's layer outputs. Screen-reader landmarks: handled at behavior-first-design, but the visual spec must not defeat landmark inference via purely visual grouping. Dynamic Type: Typography layer commits to reflow at extreme sizes. Contrast: Color layer locks 4.5:1 for body, 3:1 for large text and non-text. Reduced motion: Motion layer specifies a crossfade alternate for every directional animation. Reduced transparency: Materials layer specifies an opaque fallback for every translucent surface. The visual spec locks floors per layer; behavior-first-design and frontend-design honor them at execution time.

## Citation

Apple Inc. *Human Interface Guidelines — Foundations*. https://developer.apple.com/design/human-interface-guidelines/foundations (continuously updated; cite with retrieval date when used in research or decision documentation).
