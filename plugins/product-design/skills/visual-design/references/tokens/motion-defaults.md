# Motion defaults — duration + easing tokens

Migrated from `behavior-first-design/references/motion.md` at v0.3.0.
behavior-first-design's frequency-novelty matrix decides *whether* to fire these values; the values themselves are owned here.

## Duration tokens (per surface category)

| Surface category | Duration | Source rationale |
|---|---|---|
| Dialog enter / popover enter / tooltip enter | 150ms | Boundary-marking; small surface; Fluent 2 "fast and smooth" |
| Peek / drawer enter; new content entry | 200ms | Larger spatial traversal; eye tracks new context |
| New row slide-in (list insert) | 180ms | Spatial translation; eye tracks position change |
| Toast enter | 150ms | Fade + slight translate-up; matches dialog timing |
| Toast auto-dismiss | 5s (transient) / persistent (errors) | User-attention hold time |
| Keyboard nav / row-select / button-press / hover-reveal | 0ms | Frequent, repetitive, instant feedback required |

## Easing tokens

- `ease-out` (decelerate) for enters
- `ease-in` (accelerate) for exits
- `linear` ONLY for indeterminate progress
- **Never** spring/bounce in product UI (Rauno + Linear violation)

## CSS custom-property recommendations

```css
:root {
  /* Duration */
  --duration-instant: 0ms;       /* keyboard nav, hover reveals, row select */
  --duration-fast:    150ms;     /* dialog / popover / tooltip enter, toast enter */
  --duration-medium:  180ms;     /* new row slide-in */
  --duration-base:    200ms;     /* peek / drawer enter, new content */

  /* Easing */
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  --ease-in:  cubic-bezier(0.7, 0, 0.84, 0);
}
```

## Why these values

Fluent 2's motion chapter: "Give larger elements more time to animate than smaller elements. Aim for a fast and smooth motion without making people wait." Durations track *perceptual effort*, not surface size — a row slide-in (180ms) is a spatial translation; a dialog fade-in (150ms) is an opacity change. Peek/drawer (200ms) combines both. Row slide > dialog fade; peek > row slide. When in doubt, shorter wins; you can always slow a motion that feels abrupt, but you can't speed up one users have internalized as sluggish.

## What stays in behavior-first-design

`behavior-first-design/references/motion.md` keeps:
- Frequency-novelty matrix (when motion fires; when it doesn't)
- Reduced-motion contract (`prefers-reduced-motion`: durations → 0)
- Boundary-marker exception ("dialogs straddle the matrix")
- Conflict-with-HIG section (Rauno > HIG on frequency-based restraint)

This file owns the *values*; motion.md owns the *policy*.

## Citation

Original durations from `~/Sites/product-design/plugins/product-design/skills/behavior-first-design/references/motion.md` lines 28–40 (pre-v0.3.0). Migrated 2026-04-26 per spec `pine-research/docs/superpowers/specs/2026-04-26-visual-design-skill.md` § 8.
