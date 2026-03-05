# How to Use This Vault with Claude Code

This vault is your **persistent knowledge base** for all projects. Claude Code connects to it via the Obsidian MCP tool, giving every session:

- **Unlimited context** -- project docs live here, not in the chat window
- **Persistent memory** -- decisions, patterns, gotchas survive across sessions
- **Agent coordination** -- parallel agents read scopes and write results here
- **Cross-project learning** -- shared patterns in `_Shared/`, project-specific in folders

---

## Guides in This Template

| Guide | What it covers |
|-------|---------------|
| **This file** | Overview, setup, folder structure |
| [Token Management Guide](Token%20Management%20Guide.md) | 3-layer memory system, what goes where, anti-patterns |
| [Agent Rules](Agent%20Rules.md) | How to write agent scopes, dispatch rules, failure prevention |
| [MEMORY.md Template](MEMORY.md%20Template.md) | How to use MEMORY.md as pointer system |

---

## Setup (one-time per laptop)

1. Install Obsidian + open this vault
2. Install the Nexus Obsidian MCP plugin
3. Add the MCP server to Claude Code's config (`~/.claude/mcp_servers.json`)
4. Verify: ask Claude Code to "read my Obsidian vault" -- it should list folders

---

## Starting a New Project

1. Copy this `vault-template/` folder into your Obsidian vault, rename to your project name
2. Fill in these docs (in order):
   - `Project Overview.md` -- tech stack, goals, quick start
   - `Architecture.md` -- how code is structured
   - `Design System.md` -- colors, fonts, components (if UI project)
   - `Gotchas.md` -- things that trip you up
3. Set up your MEMORY.md using [MEMORY.md Template](MEMORY.md%20Template.md)
   - Keep it under 50 lines
   - Use pointers to Obsidian, not inline content
4. Set up your CLAUDE.md with build commands and credentials
5. Tell Claude Code: *"Read my project context from Obsidian at [ProjectName]/Project Overview.md"*

---

## The 3-Layer Memory System

```
Layer 1: CLAUDE.md          -> auto-loaded every message (~500 tokens)
  What: build commands, credentials, shell gotchas, rules
  Size: keep under 100 lines

Layer 2: MEMORY.md           -> auto-loaded every message (~300 tokens)
  What: POINTERS to Obsidian, project status, top gotchas
  Size: keep under 50 lines
  Key: use as INDEX, not storage

Layer 3: Obsidian            -> loaded on demand (0 tokens until read)
  What: architecture, design system, agent scopes, results, patterns
  Size: unlimited
  Key: read only what you need, when you need it
```

See [Token Management Guide](Token%20Management%20Guide.md) for full details.

---

## How Claude Code Uses Obsidian

### Daily coding (read context on demand)
```
You: "Fix the login bug"
Claude checks: MEMORY.md pointer -> "auth is in auth-store.ts"
Claude reads: Obsidian -> Architecture.md (auth section only)
Claude reads: auth-store.ts in codebase
Claude fixes the bug with full context
Tokens used: ~3K on demand (not 50K loaded upfront)
```

### Parallel agents (read scope, write results)
```
Orchestrator:
  1. Writes agent scopes to Obsidian (Build/ folder)
  2. Dispatches 4 agents in parallel
  3. Each agent reads its scope from Obsidian (own context window)
  4. Each agent works, then writes results to Obsidian
  5. Orchestrator reads results (~3K each), plans next wave

Total context: orchestrator 200K + 4 agents x 200K = 1M tokens of work
Orchestrator only consumed: ~12K for 4 result summaries
```

See [Agent Rules](Agent%20Rules.md) for how to write effective agent scopes.

### Knowledge capture (write patterns back)
```
Claude discovers: "This codebase has a quirk with X"
Claude writes: Obsidian -> Gotchas.md (new entry)
Every future session: reads gotcha when relevant, never hits the bug again
```

---

## Folder Structure

```
YourProject/
  Project Overview.md     <- what, why, tech stack, quick start
  Architecture.md         <- code structure, data flow, key files
  Design System.md        <- colors, fonts, components, spacing
  Gotchas.md              <- things that trip you up
  Patterns.md             <- recurring code patterns
  Decisions.md            <- ADRs (why X over Y)
  Build/
    Build Plan.md         <- what to build, agent scopes
    Progress.md           <- checklist of what's done
    Agent [Name] Scope.md <- per-agent task doc
    Agent [Name] Results.md <- per-agent output
  Bugs/                   <- known bugs, investigation notes
  Screens/                <- per-screen docs (UI projects)
```

---

## Key Principles

1. **Obsidian is the unlimited layer** -- anything too large for CLAUDE.md goes here
2. **MEMORY.md is the index** -- pointers, not content
3. **CLAUDE.md is the rulebook** -- small, critical, every-session facts
4. **Agents read from Obsidian** -- keeps orchestrator context lean
5. **Agents write to Obsidian** -- results persist across sessions
6. **Read on demand** -- don't load everything upfront, load what you need
7. **Write knowledge back** -- gotchas, patterns, decisions get documented
8. **Keep docs current** -- outdated docs are worse than no docs
