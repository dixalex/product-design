
## 2026-04-29 — v0.4.0 ship

- §5.1 per-skill structural smoke tests: PASS (SKILL.md ≤500 lines; HARD-GATE present; you-are-here prefixes count matches layer count)
- §5.3 migration validation (legacy frontmatter keys via dual-key acceptance): documented in SKILL.md; HUMAN-ONLY runtime test deferred
- §5.4 sample-spec validators: PASS (12 frontmatter keys + 6 layer H2 sections for behavior spec; analogous shapes for structural + visual)
- §5.5 writing-plans format compat (Phase A spike): PASS (run before Phase B; spike committed at f132bdd in pine-research)
- §5.2 cross-skill integration smoke test: HUMAN-ONLY; deferred to v0.4.2 / human session
- §5.6 anti-regression (clean session /plugin list test): HUMAN-ONLY; runs after Phase I push
- bfd-specific cleanup: voices/, stack-bindings/, NEXT.md, _v2-gaps.md, SMOKE-TEST.md all deleted; Common 18 components/ survives (18 files)
