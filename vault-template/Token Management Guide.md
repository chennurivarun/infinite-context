# Token Management Guide

Claude Code has a context window. Everything in it costs tokens. This guide teaches you to be efficient -- get MORE done with LESS context.

---

## The 3-Layer Memory System

```
+---------------------------------------------------+
|  Layer 1: CLAUDE.md (auto-loaded, ~200 lines)     |
|  Small, critical, every-session facts              |
|  Cost: loaded EVERY message                        |
+---------------------------------------------------+
|  Layer 2: Auto-memory (~200 lines)                 |
|  MEMORY.md + topic files                           |
|  Cost: MEMORY.md loaded every message,             |
|  topic files only when read                        |
+---------------------------------------------------+
|  Layer 3: Obsidian (unlimited)                     |
|  Architecture, design system, agent scopes,        |
|  audit results, build progress                     |
|  Cost: ZERO until explicitly read                  |
+---------------------------------------------------+
```

### What goes where

| Content | Layer | Why |
|---------|-------|-----|
| Build commands (`npm run dev`) | CLAUDE.md | Need every session, tiny |
| Credentials (demo login) | CLAUDE.md | Need every session, tiny |
| Shell gotchas (`quote zsh paths`) | CLAUDE.md | Prevents repeated errors |
| Design system colors/fonts | CLAUDE.md (summary) + Obsidian (full) | Summary in CLAUDE.md, full reference in Obsidian |
| "Project uses Zustand + TanStack Query" | MEMORY.md | Quick recall |
| "Architecture details -> see Obsidian" | MEMORY.md (pointer) | Pointer, not content |
| Full architecture doc | Obsidian | Large, read on demand |
| Agent scope (20+ lines of tasks) | Obsidian | Large, agent reads when needed |
| Audit results, build progress | Obsidian | Large, read on demand |
| Component inventory list | Obsidian | Reference, not always needed |

---

## MEMORY.md as Pointer System

MEMORY.md is loaded every message. It has a ~200 line limit. Don't waste it on content -- use it as an **index** that points to Obsidian.

### Bad (wastes tokens every message)
```markdown
# MEMORY.md
## Architecture
The app uses Expo Router with file-based routing.
Tabs are in app/(tabs)/ with index.tsx, pipeline.tsx, more.tsx.
State is managed by Zustand stores in shared/stores/.
auth-store.ts handles login with 4 demo roles...
[50 more lines of architecture details]
```

### Good (pointers, loaded cheaply)
```markdown
# MEMORY.md
## Project: My DMS App
- Expo Router + Zustand + TanStack Query + Drizzle ORM
- Light theme, Geist fonts
- Full architecture -> Obsidian: `MyProject/Architecture.md`
- Design system -> Obsidian: `MyProject/Design System.md`
- Build progress -> Obsidian: `MyProject/Build/Progress.md`
- 9 stakeholder roles, role isolation enforced
```

6 lines vs 50+. Same information accessible. 90% fewer tokens per message.

---

## When Claude Code Reads Obsidian

Obsidian content is NOT in context until Claude explicitly reads it. This is the key advantage:

```
User: "Fix the login screen"

Claude thinks: "Login = auth. Let me check architecture."
Claude reads: Obsidian -> Architecture.md (only the auth section)
Claude reads: The actual login file in codebase
Claude fixes it.

Tokens used: ~2K for Obsidian read + file read
Tokens saved: ~50K of architecture/design/build docs NOT loaded
```

### Rule: Read only what you need, when you need it

- Don't read the entire Design System doc to fix a color -- read just the colors section
- Don't read all audit results to fix one screen -- read that screen's audit
- Don't read Build Progress to add a feature -- read it only to update progress

---

## Token Budget per Session

Claude Code compresses old messages as context fills up. But you can maximize useful work:

| Action | Token cost | Notes |
|--------|-----------|-------|
| CLAUDE.md loaded | ~160/msg | Keep under 50 lines |
| MEMORY.md loaded | ~110/msg | Keep under 30 lines, pointers only |
| Read one Obsidian doc | ~800-2,500 | On demand, one-time per doc |
| Read a source file | ~1-10K | Depends on file size |
| Agent dispatch | ~2K prompt | Agent gets its own context window |
| Agent result | ~1-3K | Summary returned to orchestrator |

### Concrete savings: 80-message session

```
OLD approach (content in MEMORY.md):
  90-line MEMORY.md × 80 messages = ~32,000 tokens on overhead

NEW approach (pointers in MEMORY.md):
  30-line MEMORY.md × 80 messages =  ~8,800 tokens on overhead
  + 1 Obsidian read (Overview)    =    ~800 tokens (once)
  + 1 deeper read (Architecture)  =  ~2,500 tokens (once)
                                    ─────────
  TOTAL                           = ~12,100 tokens

SAVINGS: ~20,000 tokens per session (62%)
```

That's 20K tokens freed for actual coding instead of re-reading context.

### The agent advantage

Each dispatched agent gets its **own** context window. This is why parallel agents are powerful:

```
Orchestrator context: 200K
  +-- Agent 1 context: 200K (independent)
  +-- Agent 2 context: 200K (independent)
  +-- Agent 3 context: 200K (independent)

Total available: 800K tokens of work
Orchestrator only sees: ~3K summary per agent
```

5 agents = 5x the context capacity. The orchestrator stays lean.

---

## Anti-Patterns (Token Waste)

### 1. Stuffing CLAUDE.md
Don't put your entire design system in CLAUDE.md. Put a 5-line summary + pointer to Obsidian.

### 2. Re-reading files
If you read a file earlier in the conversation, don't read it again. It's already in context.

### 3. Inline agent prompts
Don't paste 200 lines of scope into an agent prompt. Write the scope to Obsidian, tell the agent: "Read your scope from Obsidian at [path]".

### 4. Asking for summaries of what just happened
The conversation already has it. Don't ask Claude to re-summarize -- it wastes a round trip.

### 5. Reading entire files when you need 10 lines
Use line ranges: `Read file.ts lines 50-80` instead of reading a 500-line file.

---

## Optimal Session Flow

```
1. Start session
   - CLAUDE.md auto-loads (lean, ~500 tokens)
   - MEMORY.md auto-loads (pointers, ~300 tokens)

2. User gives task
   - Claude checks MEMORY.md pointers
   - Reads relevant Obsidian doc (architecture, design system, etc.)
   - Reads relevant source files

3. Claude works
   - For simple tasks: does it directly
   - For complex tasks: dispatches agents (each gets own context)
   - Agents read their scopes from Obsidian (not inline)

4. Claude finishes
   - Writes new knowledge to Obsidian (patterns, gotchas, decisions)
   - Updates MEMORY.md pointers if new docs were created
   - Updates build progress if applicable
```
