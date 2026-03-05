# infinite-context

**Stop re-explaining your project to AI every session.** Give it a persistent brain that grows smarter with every conversation — and dispatch parallel agents that coordinate through shared knowledge, not shared context windows.

> Your computer solved the memory problem 50 years ago: small-fast-always-loaded on top, big-slow-persistent on bottom, pointers in between. We applied the same architecture to AI coding agents — and it works.

---

## The Problem

AI coding assistants have a context window limit. Every session starts fresh. You waste tokens re-explaining your project, re-reading files, and re-discovering patterns. Parallel agents can't coordinate because they don't share state.

**What if your AI assistant remembered everything — architecture, decisions, patterns, gotchas — and could dispatch 13 parallel agents that fix 64 bugs in 66 minutes with zero conflicts?**

## The Solution

Use Obsidian as a persistent, structured knowledge layer that sits between you and your AI assistant:

```
+---------------------------------------------+
|  Your Obsidian Vault (unlimited memory)     |
|                                             |
|  ProjectA/                                  |
|    Architecture.md    <- read on demand     |
|    Design System.md   <- read on demand     |
|    Build/                                   |
|      Agent Scope.md   <- agents read this   |
|      Agent Results.md <- agents write this  |
|                                             |
|  ProjectB/                                  |
|    ...same structure                        |
+--------------+------------------------------+
               | Obsidian MCP (read/write on demand)
               | Cost: 0 tokens until needed
+--------------+------------------------------+
|  Claude Code Session                        |
|                                             |
|  CLAUDE.md (100 lines, auto-loaded)         |
|  MEMORY.md (50 lines, pointers to vault)    |
|                                             |
|  +-------+ +-------+ +-------+ +-------+   |
|  |Agent 1| |Agent 2| |Agent 3| |Agent 4|   |
|  |200K   | |200K   | |200K   | |200K   |   |
|  +-------+ +-------+ +-------+ +-------+   |
+---------------------------------------------+
```

## Why This Works (stolen from your computer)

Your computer solved this exact problem 50 years ago.

A CPU has tiny, fast registers. It can't hold your entire program. So computer scientists invented a **memory hierarchy** — small-and-fast at the top, large-and-slow at the bottom, with pointers connecting them:

```
┌─────────────────────────────────────────────────────────────────┐
│                     YOUR COMPUTER                               │
│                                                                 │
│  CPU Registers    4 KB     Instant     Always loaded            │
│       ↕                                                         │
│  L1/L2 Cache     16 MB    Fast        Recently used data        │
│       ↕                                                         │
│  RAM             32 GB    On demand   Programs + open files     │
│       ↕                                                         │
│  Hard Drive      2 TB     On demand   Everything else           │
│                                                                 │
│  The CPU doesn't store everything. It LOADS what it needs,      │
│  WORKS on it, and SAVES results back. Pointers tell it          │
│  where to find things. Nothing is ever truly lost.              │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                     YOUR AI ASSISTANT                            │
│                                                                 │
│  CLAUDE.md        ~50 lines   Every msg   Rules + pointers      │
│       ↕                                                         │
│  MEMORY.md        ~30 lines   Every msg   Index to knowledge    │
│       ↕                                                         │
│  Context Window   200K tokens On demand   Current work          │
│       ↕                                                         │
│  Obsidian Vault   Unlimited   On demand   Everything else       │
│                                                                 │
│  Claude doesn't memorize everything. It READS what it needs,    │
│  WORKS on it, and WRITES discoveries back. Pointers tell it     │
│  where to find things. Nothing is ever truly lost.              │
└─────────────────────────────────────────────────────────────────┘
```

The parallel is exact:

| Computer | AI Assistant | Role |
|----------|-------------|------|
| CPU Registers | CLAUDE.md | Tiny, always loaded, critical instructions |
| L1/L2 Cache | MEMORY.md | Small index, points to bigger storage |
| RAM | Context window | Working memory — holds what you're actively using |
| Hard Drive | Obsidian vault | Persistent, unlimited, survives reboots (sessions) |
| Virtual Memory | Pointer system | Makes finite RAM feel infinite by swapping to disk |
| Multi-core CPU | Parallel agents | Multiple workers, each with own registers + cache |

**Your computer doesn't have infinite RAM. It has infinite *storage* and smart *memory management* that makes RAM feel infinite.** That's exactly what this system does for AI context windows.

When your computer runs low on RAM, it pages data to disk and loads it back when needed. When Claude's context compacts, it loses details — but the knowledge is safe in Obsidian, ready to be loaded back. **The context window is the bottleneck. Obsidian removes the bottleneck.**

> We didn't invent a bigger context window. We gave AI the same memory architecture that made computers powerful — and for the same reason: **the smartest system isn't the one with the most memory, it's the one that knows where to find what it needs.**

## The 3-Layer Memory System

### What loads automatically (every session, no effort)

| Layer | Lines | ~Tokens/msg | What Claude knows |
|-------|-------|-------------|-------------------|
| **CLAUDE.md** | ~50 | ~160 | Build commands, hard rules, "read Obsidian on first task" |
| **MEMORY.md** | ~30 | ~110 | Pointers to all docs, current status, blockers |
| **Total overhead** | **~80** | **~270** | **Enough to start smart, not enough to waste** |

### What loads on demand (zero cost until needed)

| Obsidian Doc | ~Tokens | When Claude reads it |
|-------------|---------|---------------------|
| Project Overview | ~800 | First task (auto, CLAUDE.md says to) |
| Architecture | ~2,500 | When touching code structure, DB, routing |
| Gotchas | ~1,600 | When something might break |
| Patterns | ~1,400 | When writing new code |
| Decisions | ~1,500 | When making architecture choices |
| Build Plan | ~2,400 | When planning what to build next |

The key insight: **MEMORY.md is an index, not storage.** It points to Obsidian docs. Claude reads them only when needed.

```markdown
# Bad MEMORY.md (50+ lines of content loaded every message)
## Architecture
The app uses Expo Router with file-based routing.
Tabs are in app/(tabs)/ with index.tsx, pipeline.tsx...
State is managed by Zustand stores in shared/stores/...
[50 more lines eating tokens every single message]
```

```markdown
# Good MEMORY.md (6 lines of pointers, same information accessible)
## MyProject
- Stack: Next.js + Prisma + tRPC
- Architecture -> Obsidian: `MyProject/Architecture.md`
- Design system -> Obsidian: `MyProject/Design System.md`
- Build progress -> Obsidian: `MyProject/Build/Progress.md`
- Gotcha: always run `prisma generate` after schema changes
```

6 lines vs 50+. Same information accessible. **90% fewer tokens per message.**

### Slash commands (optional power-ups)

Add these to your CLAUDE.md for instant context loading:

| Command | What it does |
|---------|-------------|
| `/context` | Reads Overview + Architecture + Gotchas — full context in one shot |
| `/audit` | Reads audit report + build plan — shows what's broken and priorities |
| `/learn` | Writes session discoveries back to Obsidian — next session starts smarter |

These are just [Claude Code custom slash commands](https://docs.anthropic.com/en/docs/claude-code) that trigger Obsidian MCP reads. See the [Setup Guide](vault-template/Setup%20Guide.md) for how to create them.

## Parallel Agent Orchestration

The real power: dispatch multiple agents that coordinate through Obsidian.

```
Orchestrator Session (your main Claude Code window)
  |
  |-- Writes agent scopes to Obsidian
  |
  |-- Wave 1: Foundation Agent (alone)
  |   +-- Fixes shared code all other agents depend on
  |   +-- Writes results to Obsidian
  |
  |-- Wave 2: 4 Feature Agents (parallel)
  |   |-- Agent A reads scope from Obsidian -> works -> writes results
  |   |-- Agent B reads scope from Obsidian -> works -> writes results
  |   |-- Agent C reads scope from Obsidian -> works -> writes results
  |   +-- Agent D reads scope from Obsidian -> works -> writes results
  |
  +-- Orchestrator reads all results -> plans next wave
```

Each agent gets its **own 200K context window**. 5 agents = **1M tokens of work capacity**, but the orchestrator only sees ~3K summary per agent.

### The 5 Agent Rules

1. **Agents own flows, not files** — understand the user journey, not just a file list
2. **Non-overlapping file ownership** — two agents editing the same file = conflicts
3. **Read scope from Obsidian** — keeps orchestrator context lean
4. **Write results to Obsidian** — persistence across sessions
5. **Foundation before parallel** — fix shared code first, then parallelize

## How It Solves Every Claude Memory Problem

| Problem | Old way | With infinite-context |
|---------|---------|----------------------|
| "Claude forgot what we built" | Re-explain every session | CLAUDE.md auto-reads Obsidian on first task |
| "Context window full of old stuff" | 90+ line MEMORY.md burning 400 tokens/msg | 30-line pointers, ~110 tokens/msg |
| "Agent doesn't know the codebase" | Paste context inline (bloats orchestrator) | Agent reads from Obsidian (own 200K window) |
| "Learned something but lost it next session" | Hope MEMORY.md picks it up | Write to Obsidian explicitly (or `/learn`) |
| "Need full context NOW" | Explain everything manually | Type `/context` — reads 3 docs in one shot |
| "Claude uses wrong library/pattern" | Correct it every time | Hard rule in CLAUDE.md (auto-loaded) |

### Token savings in practice

For a typical **80-message session**:

```
OLD:  90-line MEMORY.md × 80 messages  = ~32,000 tokens on memory overhead
NEW:  30-line MEMORY.md × 80 messages  =  ~8,800 tokens on memory overhead
      + 1 Obsidian read (Overview)      =    ~800 tokens (once)
      + 1 deeper read (Architecture)    =  ~2,500 tokens (once)
                                         ─────────
      TOTAL                             = ~12,100 tokens

SAVINGS: ~20,000 tokens per session (62%)
```

That's 20K tokens freed up for **actual work** instead of re-reading context Claude already has.

## Real-World Results

Tested on a **687-user-story React Native app** (180+ screens, 9 user roles):

| Metric | Result |
|--------|--------|
| Build waves | 9 waves, 101 deliverables |
| Audit | 6 parallel agents, 102 issues found across 180+ screens |
| Fix waves | 13 agents across 5 waves |
| Issues fixed | **64 in ~66 minutes** |
| TypeScript errors after | **0** |
| File conflicts between agents | **0** |
| Orchestrator token usage | ~30K (while coordinating 1M+ agent work) |

## Quick Start

### 1. Prerequisites
- [Obsidian](https://obsidian.md/) installed
- [Claude Code](https://claude.ai/code) (or any AI assistant with MCP support)
- An Obsidian MCP plugin configured (e.g., [Nexus Obsidian](https://github.com/nexus-obsidian))

### 2. Copy the vault template
```bash
git clone https://github.com/varun/infinite-context.git
cp -r infinite-context/vault-template/ /path/to/your/obsidian/vault/YourProject/
```

### 3. Fill in your project docs
Start with `Project Overview.md`, then `Architecture.md`, then `Design System.md`.

### 4. Add Obsidian pointers to your CLAUDE.md (in your repo root)

This is the critical step that makes it automatic. Add these lines to your project's `CLAUDE.md`:

```markdown
## Obsidian Knowledge Base
This project's full documentation lives in Obsidian. Before starting any task,
read the relevant doc using the Obsidian MCP tool:
- Architecture: `MyProject/Architecture.md`
- Design System: `MyProject/Design System.md`
- Gotchas: `MyProject/Gotchas.md`
- Build Progress: `MyProject/Build/Progress.md`

When you discover something new (gotcha, pattern, decision), write it back to
the relevant Obsidian doc. The vault should grow smarter with every session.
```

This tells Claude Code: **"Your memory lives in Obsidian. Read it. Write to it."**

### 5. Set up MEMORY.md with pointers (not content)

Your auto-memory file should be an **index** that points to Obsidian:

```markdown
# MyProject -- Memory
- Stack: React + Zustand + Prisma
- Architecture -> Obsidian: `MyProject/Architecture.md`
- Design system -> Obsidian: `MyProject/Design System.md`
- Build progress -> Obsidian: `MyProject/Build/Progress.md`
- Gotcha: always quote zsh paths with parens
```

50 lines max. Everything else lives in Obsidian (unlimited).

### 6. Start coding -- it's automatic now

Every new session:
1. CLAUDE.md auto-loads -> sees "check Obsidian for context"
2. MEMORY.md auto-loads -> sees pointers to Obsidian docs
3. Claude reads relevant Obsidian docs **on demand** (only what's needed)
4. Claude works with full project context
5. Claude writes new knowledge back to Obsidian

**The vault grows smarter with every session. That's infinite context.**

> For the complete setup walkthrough with troubleshooting, see the [Setup Guide](vault-template/Setup%20Guide.md).

## What's Included

```
vault-template/
  Setup Guide.md                <- COMPLETE SETUP WALKTHROUGH
  How to Use This Vault.md      <- Overview + principles
  Token Management Guide.md     <- 3-layer system, anti-patterns, token costs
  Agent Rules.md                <- 5 rules, scope templates, failure prevention
  MEMORY.md Template.md         <- How to use pointers (not storage)
  CLAUDE.md Template.md         <- What goes in your repo's CLAUDE.md
  Project Overview.md           <- Fill-in template
  Architecture.md               <- Fill-in template
  Design System.md              <- Fill-in template
  Gotchas.md                    <- Fill-in template
  Patterns.md                   <- Fill-in template
  Decisions.md                  <- Fill-in template (ADRs)
  Build/
    Build Plan.md               <- Build order + agent scope guide

examples/
  real-world-example/           <- Real-world example with full stats
```

## Key Guides

| Guide | What you'll learn |
|-------|-------------------|
| [Setup Guide](vault-template/Setup%20Guide.md) | **Start here** -- complete step-by-step setup with exact config files |
| [Token Management](vault-template/Token%20Management%20Guide.md) | The 3-layer system, what goes where, anti-patterns that waste tokens |
| [Agent Rules](vault-template/Agent%20Rules.md) | How to write agent scopes, dispatch parallel waves, prevent failures |
| [MEMORY.md as Pointers](vault-template/MEMORY.md%20Template.md) | Turn MEMORY.md into an index (not a dumping ground) |
| [CLAUDE.md Template](vault-template/CLAUDE.md%20Template.md) | What belongs in your repo's auto-loaded instructions |
| [Real Example](examples/real-world-example/) | How this workflow built a 180+ screen app |

## How It Works (the feedback loop)

```
Session 1: You set up the project
  Claude reads: Architecture.md, Design System.md
  Claude discovers: "oh, this codebase has a quirk with X"
  Claude writes: Gotchas.md -> "watch out for X"

Session 2: You fix a bug
  Claude reads: Gotchas.md -> already knows about X
  Claude discovers: "the team decided to use Y pattern"
  Claude writes: Decisions.md -> "chose Y because Z"

Session 3: You add a feature
  Claude reads: Decisions.md -> knows why Y was chosen
  Claude reads: Patterns.md -> follows existing conventions
  Claude discovers: "this API has a rate limit"
  Claude writes: Gotchas.md -> "API rate limit: 100/min"

Session 50: New developer joins
  Claude reads: everything above
  Has 50 sessions worth of accumulated knowledge
  Knows every gotcha, every decision, every pattern
  Zero ramp-up time
```

**Each session makes the vault smarter. The 50th session has 50 sessions of knowledge -- instantly.**

## Works With

- **Claude Code** (primary — has MCP support + Agent tool for parallel dispatch)
- **Cursor** (MCP support for Obsidian reads)
- **Windsurf** (MCP support)
- **Any AI assistant with MCP support**
- **Manual workflow** (human reads Obsidian docs, pastes relevant context into any AI chat)

## Why Obsidian?

- **Local-first** — your project knowledge stays on your machine
- **Markdown** — no vendor lock-in, just `.md` files
- **Linked** — `[[wikilinks]]` connect architecture to decisions to patterns
- **MCP plugins** — Claude Code can read/write directly
- **Free** — no subscription needed for this workflow
- **You probably already use it** — many developers already have an Obsidian vault

## Contributing

This is a methodology, not a library. Contributions welcome:
- Better templates for specific project types (backend API, mobile app, web SPA, monorepo)
- Integration guides for other AI assistants (Cursor, Windsurf, Copilot)
- Real-world examples (sanitized)
- Token optimization techniques
- Translations

## License

MIT — use it however you want.

---

*Computers don't have infinite RAM — they have memory hierarchies that make RAM feel infinite. We gave AI the same trick. Built by shipping a real product with 13 parallel agents, not in a lab.*
