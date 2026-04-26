# product-design plugin

A Claude Code plugin bundling skills for **product design thinking** across the full Garrett/Elements-of-UX funnel.

## Skills included

| Skill | Invocation | Layer | Shape | Version |
|---|---|---|---|---|
| `behavior-first-design` | `/product-design:behavior-first-design` | Skeleton (interaction, keyboard, focus, motion) | Reference library | v1 |
| `product-architecture` | `/product-design:product-architecture` | Intent → Structure → Flows → Disclosure | Process / guided dialogue | v1 |
| `visual-design` | `/product-design:visual-design` | Surface (typography, color, density, polish) | Reference library | v1 |

Skills compose: run `product-architecture` → handoff brief → `behavior-first-design` per surface → styled with `visual-design`.

## Install (local, for Claude Code)

Claude Code discovers plugins at `~/.claude/plugins/`. Symlink this repo:

```bash
ln -s "$(pwd)" ~/.claude/plugins/product-design
```

Restart Claude Code (or re-open a session). The skills will activate on their trigger phrases.

## Install (git clone + symlink)

If you're a collaborator testing this:

```bash
cd ~/Sites
git clone <this-repo-url> product-design
ln -s "$HOME/Sites/product-design" ~/.claude/plugins/product-design
```

## Canonical references

- Garrett's *Elements of User Experience* (5-plane model)
- Dan Brown's *8 Principles of Information Architecture* (2010)
- Sophia V Prater's *Object-Oriented UX*
- Jon Yablonski's *Laws of UX* (cross-referenced from `behavior-first-design/references/foundations.md`)
- Nielsen Norman Group heuristics, IDEO 3-lens, Christensen JTBD
- Domain-specific voices: Linear, Superhuman, Rauno Freiberg, Eagle (Pine's internal)

Each skill cites its sources inline.

## For plugin authors adding executable tooling

When adding scripts, agents, or hooks in a future version, use `${CLAUDE_PLUGIN_ROOT}` to reference intra-plugin paths portably. `${CLAUDE_PLUGIN_DATA}` for writable state. v1 is markdown-only; not used yet.

## License

MIT. Use freely. Attribute when you can.
