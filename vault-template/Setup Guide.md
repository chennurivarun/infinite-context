# Setup Guide -- Make Claude Code Use Obsidian Automatically

This is the step-by-step setup that makes infinite-context work. After this, every new Claude Code session automatically has access to your entire project knowledge in Obsidian -- no manual "read from Obsidian" needed.

---

## Step 1: Install Obsidian MCP Server

Claude Code connects to Obsidian via MCP (Model Context Protocol). You need an Obsidian MCP plugin.

### Option A: Nexus Obsidian (recommended)
```bash
# Install the Nexus Obsidian plugin in your Obsidian vault
# 1. Open Obsidian -> Settings -> Community Plugins -> Browse
# 2. Search "Nexus" or your preferred Obsidian MCP plugin
# 3. Install and enable it
```

### Option B: Any Obsidian MCP server
Any MCP server that can read/write Obsidian files works. The key operations needed:
- Read file by path
- Write file by path
- Search/list files
- Create folders

## Step 2: Configure Claude Code to Connect

Add the MCP server to Claude Code's config:

```bash
# Edit your MCP servers config
# Location: ~/.claude/mcp_servers.json (or check Claude Code settings)
```

Add your Obsidian MCP server entry. The exact format depends on which plugin you use. Example:

```json
{
  "nexus-obsidian": {
    "command": "your-mcp-server-command",
    "args": ["--vault", "/path/to/your/obsidian/vault"]
  }
}
```

### Verify it works
Open Claude Code and ask:
> "List files in my Obsidian vault"

If it returns your vault contents, you're connected.

---

## Step 3: Create Your Project Folder in Obsidian

Copy the vault template into your Obsidian vault:

```bash
cp -r vault-template/ "/path/to/your/obsidian/vault/MyProject/"
```

Or manually create the folder in Obsidian and copy the `.md` files.

Fill in at minimum:
1. `Project Overview.md` -- tech stack, codebase path, quick start commands
2. `Architecture.md` -- how the code is structured
3. `Gotchas.md` -- things that trip you up

The other files (Design System, Patterns, Decisions) can be filled in as you go.

---

## Step 4: Set Up CLAUDE.md (auto-loaded every session)

This file lives in your **project repo root** (not in Obsidian). Claude Code loads it automatically every session.

**Key rule: Keep it under 100 lines. Point to Obsidian for details.**

Create `/path/to/your/project/CLAUDE.md`:

```markdown
# MyProject -- Claude Instructions

## Obsidian Knowledge Base
This project's full documentation lives in Obsidian. Before starting any task,
read the relevant doc:
- Architecture: read `MyProject/Architecture.md` from Obsidian
- Design System: read `MyProject/Design System.md` from Obsidian
- Gotchas: read `MyProject/Gotchas.md` from Obsidian
- Build Progress: read `MyProject/Build/Progress.md` from Obsidian

Use the Obsidian MCP tool to read these. Do NOT rely on chat history for
project context -- always check Obsidian for the latest.

## Quick Commands
```bash
npm install        # install deps
npm run dev        # start dev server
npm run typecheck  # TypeScript check
npm test           # run tests
```

## Design System (summary -- full in Obsidian)
- Theme: light
- Primary font: Inter
- Mono font: JetBrains Mono
- Colors: bg=#FFFFFF, text=#1A1A1A, accent=#3B82F6
- Full details -> Obsidian: `MyProject/Design System.md`

## Demo Credentials
| Role | Login | Password |
|------|-------|----------|
| Admin | admin@example.com | admin123 |
| User | user@example.com | user123 |

## Shell Gotchas
- Always quote paths with spaces
- Run typecheck from project root

## Rules
- Read files before editing
- Match existing code style
- When you discover a new gotcha, write it to Obsidian: `MyProject/Gotchas.md`
- When you make an architecture decision, write it to Obsidian: `MyProject/Decisions.md`
```

### Why this works
The line `"Before starting any task, read the relevant doc"` tells Claude Code to check Obsidian automatically. It becomes a habit -- Claude reads the architecture doc before making changes, checks gotchas before debugging, and writes new knowledge back.

---

## Step 5: Set Up MEMORY.md (persistent across sessions)

This file lives in Claude Code's auto-memory directory (NOT in your repo, NOT in Obsidian).

Location: `~/.claude/projects/<project-hash>/memory/MEMORY.md`

Claude Code auto-creates this. You can also write to it manually or tell Claude to update it.

**Key rule: Under 50 lines. Pointers only.**

```markdown
# MyProject -- Memory

## Project
- E-commerce platform built with Next.js 14 + Prisma + tRPC
- Codebase: `/Users/me/projects/myproject`
- Obsidian docs: `MyProject/` in vault

## Architecture (read from Obsidian when needed)
- Full docs -> Obsidian: `MyProject/Architecture.md`
- Key stores: useAuthStore, useCartStore, useProductStore
- Key routes: /app/(shop)/, /app/(admin)/, /app/api/

## Design System (read from Obsidian when needed)
- Full docs -> Obsidian: `MyProject/Design System.md`
- Light theme, Inter font, Tailwind CSS
- Primary: blue-600, radius: 8px

## Build Status
- Progress -> Obsidian: `MyProject/Build/Progress.md`
- Current: building checkout flow
- Blocker: Stripe webhook testing

## Gotchas (inline -- saves every session)
- Prisma: run `npx prisma generate` after schema changes
- Next.js: server components can't use hooks
- Env: .env.local not committed, copy from .env.example

## Conventions
- Always use tRPC procedures, never raw fetch
- Use Zustand for client state, tRPC for server state
- Components in /components, pages in /app
```

### How the pointer system works

```
Session starts:
  1. CLAUDE.md loads (100 lines, ~500 tokens)
     -> tells Claude "check Obsidian for architecture"
  2. MEMORY.md loads (50 lines, ~300 tokens)
     -> tells Claude "Architecture is at MyProject/Architecture.md"

User says: "Fix the cart bug"
  3. Claude sees pointer: "Architecture -> Obsidian: MyProject/Architecture.md"
  4. Claude reads ONLY the architecture doc (~3K tokens, one-time)
  5. Claude reads the cart store file
  6. Claude fixes the bug with full context

Total cost: 800 (auto) + 3K (on demand) = 3.8K tokens
Without pointers: 50K+ tokens loaded upfront every message
```

---

## Step 6: Tell Claude to Write Knowledge Back

The system grows smarter over time. Add this rule to CLAUDE.md:

```markdown
## Knowledge Management
When you discover something important during this session:
- New gotcha -> write to Obsidian: `MyProject/Gotchas.md`
- New pattern -> write to Obsidian: `MyProject/Patterns.md`
- Architecture decision -> write to Obsidian: `MyProject/Decisions.md`
- Bug investigation -> write to Obsidian: `MyProject/Bugs/[bug-name].md`

When you complete a build task:
- Update progress -> Obsidian: `MyProject/Build/Progress.md`
```

This creates a **feedback loop**: Claude reads knowledge -> works -> discovers new knowledge -> writes it back -> next session reads it.

---

## Step 7: Parallel Agents (optional, for large tasks)

When you have a big task (many files, multiple independent fixes), use parallel agents:

### Write agent scopes to Obsidian
```
You: "Write an agent scope to Obsidian at MyProject/Build/Agent A Scope.md with these tasks: [...]"
```

### Dispatch agents that read from Obsidian
```
You: "Dispatch 3 parallel agents. Each should:
1. Read its scope from Obsidian at MyProject/Build/Agent [X] Scope.md
2. Execute all tasks
3. Write results to Obsidian at MyProject/Build/Agent [X] Results.md"
```

### Collect results
```
You: "Read all agent results from Obsidian: MyProject/Build/Agent A Results.md, Agent B Results.md, Agent C Results.md"
```

See [Agent Rules](Agent%20Rules.md) for the full parallel agent workflow.

---

## The Complete Flow (every session)

```
1. You open Claude Code in your project
   |
   v
2. CLAUDE.md auto-loads (~500 tokens)
   Contains: "check Obsidian for architecture/design/gotchas"
   |
   v
3. MEMORY.md auto-loads (~300 tokens)
   Contains: pointers like "Architecture -> Obsidian: MyProject/Architecture.md"
   |
   v
4. You give a task: "Add user profile page"
   |
   v
5. Claude reads relevant Obsidian docs (on demand, ~3-5K tokens)
   - Architecture.md (routing, data flow)
   - Design System.md (colors, components)
   - Patterns.md (how similar pages are built)
   |
   v
6. Claude builds the feature with full context
   |
   v
7. Claude writes back to Obsidian:
   - New pattern discovered -> Patterns.md
   - New gotcha found -> Gotchas.md
   - Progress updated -> Build/Progress.md
   |
   v
8. Next session starts at step 1 with MORE knowledge
```

**The vault grows smarter with every session. That's infinite context.**

---

## Troubleshooting

### Claude doesn't read from Obsidian automatically
Make sure CLAUDE.md contains the instruction to check Obsidian. The key line:
```
Before starting any task, read the relevant doc from Obsidian using the MCP tool.
```

### Claude reads too much from Obsidian (slow)
Your docs might be too large. Split them into sections. Claude should read the relevant section, not the entire doc.

### Pointers in MEMORY.md are stale
When you create new Obsidian docs, tell Claude: "Update MEMORY.md to add a pointer to the new [X] doc at [path]"

### Agent doesn't write results to Obsidian
Always include this in agent prompts: "Write results to Obsidian at [path]. This is MANDATORY."

### MCP connection lost mid-session
Restart Claude Code. The MCP connection will re-establish. Your Obsidian docs are just files -- nothing is lost.
