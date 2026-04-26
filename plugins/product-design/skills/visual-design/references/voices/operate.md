# Operate — sales-CRM product (operate.so), Pine Event's reference baseline

- **Source:** operate.so (homepage), design.operate.so
- **Captured:** 2026-04-26
- **Layer relevance:** Mood, Color, Typography, Spacing, Polish

## Aesthetic profile

Operate sits between Linear's clinical restraint and consumer-product warmth — closer to Linear, but warmer. It's a serious sales tool that doesn't signal "enterprise" and doesn't perform friendliness. The mood is founder-empathy: every surface detail reflects a judgment call, and the judgment shows. The hand-named color palette — graphpaper, ember, blueberry, copper — is the clearest signal: these are not token-library system colors. They are named the way a person names things.

The visual language follows from the information architecture. Operate treats Customer Feedback as a peer object, not a sub-attribute — that choice materializes as a status system spanning multiple hues. Palette breadth exists because the domain demands it. The custom easing curve `--ease-elegant` and tiered `--dynamic-shadow-*` system carry the same logic: deliberate choices, not defaults.

Typography does the same work. Muoto Variable (205TF) is a humanist sans with a geometric backbone — similar to Inter in utility, distinct in character. The base is `--text-base: 13.5px` — not 14px, not 16px — paired with `--font-weight-book: 450`, an intermediate weight only variable fonts expose. Legible, dense, clearly tuned.

## Per-layer treatment

### Mood

- Decision: refined + warm + craft-visible. A tool that has been thought about, not configured from defaults.
- Why it works: founder-empathy products earn trust through visible care. Operate's audience — founders, sales leaders — reads craft as competence.
- When NOT to copy: enterprise procurement contexts where hand-crafted details read "small team / risky." High-volume consumer surfaces where craft is invisible at scale.

### Color

- Decision: hand-named semantic palette — graphpaper `#87c295` (green), ember `#ea5a13` (orange), blueberry `#6358b7` (blue-purple), copper `#b85725` (warm brown) — each carrying a distinct status role. Green is the brand anchor (`--color-brand`, `--color-link`, `--color-success`). Green-tinted neutrals throughout (not pure gray). Light-mode-first; not dark-mode-primary.
- Why it works: palette breadth accommodates complex status semantics without collapsing to a single accent. Hand-named colors signal intentionality — "graphpaper" is not a design-system name. Green-tinted neutrals keep the brand hue present at low intensity across every surface.
- When NOT to copy: products without complex status semantics (Linear-style single-accent discipline wins there). Light-mode-first architecture locks out dark-mode-primary audiences.

### Typography

- Decision: Muoto Variable (205TF) at `--text-base: 13.5px`. Variable axis carries body (`--font-weight-book: 450`) through display (`--font-weight-medium: 500`) without a second typeface. Scale multiplier-derived from base: xs ×0.889, md ×1.25, lg ×1.4.
- Why it works: distinctive without exotic; the variable axis means no second-font licensing cost and enables the `450` book weight as the reading weight. 13.5px signals hand-tuning.
- When NOT to copy: surfaces needing mass-market readability fallbacks or heavy localization (Muoto is Latin-centric). Requires a 205TF commercial license; substitute a variable-axis humanist (Geist, Söhne) if unavailable.

### Spacing

- Decision: 4pt grid (`--spacing: .25rem`), not 8pt. List rows 32–36px. Dense — sales workflows are record-intensive.
- Why it works: 4pt granularity accommodates the 13.5px type base without awkward rounding. Density matches the workflow: users need data per screen, not breathing room.
- When NOT to copy: products with established 8pt-grid component libraries (Material, shadcn). Touch-primary surfaces; read-mostly content surfaces.

### Polish

- Decision: `--ease-elegant` — a 100-stop `linear()` curve with a subtle overshoot (~1.03 at 42%, settling to 1.0) — paired with `--duration-elegant: .45s`. Layered `--dynamic-shadow-*` system: five numbered tiers (`100`–`500`) plus role aliases (`button → 200`, `tooltip/peek → 400`, `pop-off → 500`). Every tier stacks a hairline `--dynamic-shadow-border` beneath blur layers.
- Why it works: the custom easing curve is a signature that differentiates every transition from default cubic-bezier feel. Named shadow tiers with role aliases make the system composable; the hairline-in-every-tier ensures edge definition without a separate border rule.
- When NOT to copy: products at scale where custom easing requires cross-team education. Performance-constrained surfaces where `.45s` on frequent micro-interactions accumulates to noticeable lag.

## Citation

- operate.so (homepage)
- design.operate.so (design team posts)
- Cross-reference: `tokens/operate-extracted.md` for canonical token values (palette hex, shadow stack, easing curve, type scale).
