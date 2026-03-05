# MEMORY.md Template

Copy this structure into your project's auto-memory MEMORY.md file.
Keep it LEAN -- this is loaded every single message.

---

```markdown
# [Project Name] -- Memory

## Project
- [One-line description]
- Stack: [framework] + [state] + [db] + [key libs]
- Codebase: `/path/to/project`

## Architecture (pointer)
- Full docs -> Obsidian: `[Project]/Architecture.md`
- Key stores: [list main stores]
- Key routes: [list main routes/pages]

## Design System (pointer)
- Full docs -> Obsidian: `[Project]/Design System.md`
- Theme: [light/dark]
- Fonts: [family names]
- Primary color: [hex]

## Build Status (pointer)
- Progress -> Obsidian: `[Project]/Build/Progress.md`
- Current phase: [what's being worked on]
- Blockers: [any known blockers]

## Gotchas (inline -- these save every session)
- [Most common gotcha 1]
- [Most common gotcha 2]
- [Most common gotcha 3]

## Conventions
- [Key convention 1, e.g., "always use store, never import mock data directly"]
- [Key convention 2]
```

---

## What makes a good MEMORY.md

1. **Under 50 lines** -- it's loaded every message
2. **Pointers to Obsidian** for anything longer than 2 lines
3. **Inline only the top 3-5 gotchas** that save the most time
4. **Updated when things change** -- stale pointers are worse than none
5. **No duplicate of CLAUDE.md** -- they serve different purposes

## MEMORY.md vs CLAUDE.md

| | CLAUDE.md | MEMORY.md |
|---|---|---|
| **Checked into git** | Yes | No (user-local) |
| **Shared with team** | Yes | No (personal) |
| **Content** | Build commands, credentials, rules | Pointers, status, preferences |
| **Updated by** | Human (committed) | Claude (auto-memory) |
| **Purpose** | "How to work in this repo" | "What Claude remembers" |
