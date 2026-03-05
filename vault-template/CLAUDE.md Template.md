# CLAUDE.md Template

Put this in your project root. It's auto-loaded every session.
Keep it LEAN -- under 100 lines. Big docs go in Obsidian.

---

```markdown
# [Project Name] -- Claude Project Instructions

## Quick Start
```bash
npm install        # install deps
npm run dev        # start dev server
npm run typecheck  # check TypeScript
npm test           # run tests
```

## Key Architecture
- [Framework]: [brief note]
- State: [library] in [path]
- API: [pattern] in [path]
- Routing: [library], file-based in [path]
- Full architecture -> Obsidian: `[Project]/Architecture.md`

## Design System (summary)
- Theme: [light/dark]
- Primary font: [name]
- Mono font: [name]
- Key colors: bg=[hex], text=[hex], accent=[hex]
- Full design system -> Obsidian: `[Project]/Design System.md`

## Demo Credentials
| Role | Login | Password |
|------|-------|----------|
| | | |

## Shell Gotchas
- [e.g., "zsh: always quote paths with parens"]
- [e.g., "TypeScript: npx tsc --noEmit --skipLibCheck"]

## Rules
- [e.g., "Use Fonts.numeric for all numbers"]
- [e.g., "Never use @gorhom/bottom-sheet, use Modal"]
- [e.g., "Match existing code style, don't refactor"]
```

---

## Tips

1. **Don't duplicate Obsidian here** -- point to it
2. **Credentials are fine here** -- this is for dev/demo only
3. **Shell gotchas save the most time** -- Claude hits them every session
4. **Rules prevent repeated mistakes** -- add one whenever Claude gets something wrong twice
