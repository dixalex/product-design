
## 2026-04-29 — v0.4.0 ship

- §5.1 per-skill structural smoke tests: PASS (SKILL.md ≤500 lines; HARD-GATE present; you-are-here prefixes count matches layer count)
- §5.3 migration validation (legacy frontmatter keys via dual-key acceptance): documented in SKILL.md; HUMAN-ONLY runtime test deferred
- §5.4 sample-spec validators: PASS (12 frontmatter keys + 6 layer H2 sections for behavior spec; analogous shapes for structural + visual)
- §5.5 writing-plans format compat (Phase A spike): PASS (run before Phase B; spike committed at f132bdd in pine-research)
- §5.2 cross-skill integration smoke test: HUMAN-ONLY; deferred to v0.4.2 / human session
- §5.6 anti-regression (clean session /plugin list test): HUMAN-ONLY; runs after Phase I push
- bfd-specific cleanup: voices/, stack-bindings/, NEXT.md, _v2-gaps.md, SMOKE-TEST.md all deleted; Common 18 components/ survives (18 files)

## 2026-04-30 — v0.4.1 ship

- Per-skill structural smoke: PASS (no regressions from v0.4.0)
- Mermaid block presence (PA + bfd): PASS (2 blocks each in templates; 4 layer files updated)
- SVG block presence (visual-design Color + Typography): PASS (2 SVGs in template; 2 layer files updated)
- Ousterhout canon: present at PA canon/ (994 words); cross-ref from structure.md OK
- Backward-compat: v0.4.0 specs parse cleanly under v0.4.1 (additive only — Mermaid/SVG are markdown extensions, no schema changes)

Status: PASS. Local commit; hold push for C2 fresh-session integration round.
