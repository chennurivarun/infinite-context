# Agent Rules -- How to Write Effective Agent Scopes

This guide covers how to dispatch parallel agents that actually work -- the rules that prevent conflicts, context overflow, and lost work.

---

## The 5 Rules

### Rule 1: Agents own FLOWS, not FILES

Bad: "Agent A: fix file1.tsx, file2.tsx, file3.tsx"
Good: "Agent A: fix the entire booking flow (list -> detail -> create -> confirm)"

Why: When agents own files, they make locally-correct changes that break the flow. When they own flows, they understand the user journey and keep it consistent.

### Rule 2: Non-overlapping file ownership

If two agents edit the same file, one overwrites the other. Always.

```
Agent A owns: pipeline.tsx, more.tsx
Agent B owns: index.tsx (tabs)
Agent C owns: admin/*.tsx

NEVER: Agent A and B both edit index.tsx
```

If a flow crosses files, one agent owns the whole flow.

### Rule 3: Agents read scope from Obsidian, not inline

Bad (wastes orchestrator tokens):
```
Agent prompt: "Fix these 50 issues: [200 lines of scope pasted inline]"
```

Good (efficient):
```
Agent prompt: "Read your scope from Obsidian at [Project]/Build/Agent A Scope.md, then execute all tasks."
```

Why: The orchestrator's context window stays lean. The agent reads the full scope in its own context window.

### Rule 4: Agents write results to Obsidian before finishing

Every agent prompt must include:
```
"When done, write your results to Obsidian at [Project]/Build/[Agent] Results.md.
Include: tasks completed, files modified, errors found, verification output.
This is MANDATORY -- do not finish without writing results."
```

Why: If an agent finishes without writing results, the orchestrator has no record. The work is invisible.

### Rule 5: Foundation before parallel

If multiple agents depend on shared data (stores, types, mock data), run ONE foundation agent first to fix the shared layer. Then run parallel agents.

```
Step 1: Foundation Agent (alone) -- fixes shared stores, types, mock data
Step 2: Feature Agents (parallel) -- each builds on the fixed foundation
```

Why: If parallel agents each "fix" the shared store their own way, you get conflicts.

---

## Agent Scope Template

Write this to Obsidian for each agent:

```markdown
# Agent [Name] -- [Purpose]

## Context
[What this agent needs to know about the project/codebase]
[Point to relevant Obsidian docs: Architecture.md, Design System.md]

## Codebase
`/path/to/project`

## Files You Own (ONLY touch these)
- path/to/file1.tsx
- path/to/file2.tsx
- path/to/store.ts

## Tasks
### Task 1 -- [Title]
**Problem:** [what's wrong, with file:line references]
**Fix:** [exact instructions, code snippets if helpful]

### Task 2 -- [Title]
**Problem:**
**Fix:**

## Rules
- Read each file before editing
- Do NOT modify files outside your scope
- Match existing code style
- Run TypeScript check after all changes
- [Project-specific rules: design system, naming, etc.]

## Verification
[Commands to run after all changes]
- `npm run typecheck` -- must show 0 errors
- `grep -rn "pattern" src/` -- must show 0 results

## Output
Write results to: `[Project]/Build/[Agent] Results.md`
Format:
- Tasks completed (checklist)
- Files modified (list)
- Errors found (count)
- Verification output (paste)
```

---

## Orchestrator Prompt Template

The orchestrator (your main Claude Code session) dispatches agents like this:

```
You are Agent [Name] for [Project].
Codebase: /path/to/project

Step 1: Read your scope from Obsidian
Use the Obsidian MCP tool to read: "[Project]/Build/[Agent] Scope.md"

Step 2: Execute all tasks in the scope document

Step 3: Run verification commands listed in the scope

Step 4: Write results to Obsidian at "[Project]/Build/[Agent] Results.md"
This is MANDATORY -- do not finish without writing results.
```

Keep this prompt SHORT. All the detail lives in Obsidian, not in the prompt.

---

## Parallel Dispatch Rules

### When to parallelize
- 2+ independent tasks that don't share files
- Each task takes 5+ minutes (not worth parallelizing trivial tasks)
- You have clear scope boundaries

### When to serialize
- Tasks share files (one must finish before the other starts)
- Task B depends on Task A's output
- You're unsure about scope boundaries (serialize first, parallelize later)

### Wave pattern
```
Wave 1: Foundation (1 agent, alone)
  +-- fixes shared data layer

Wave 2: Features (N agents, parallel)
  +-- each owns a complete flow
  +-- all read from the fixed foundation

Wave 3: Integration (1-2 agents)
  +-- wires cross-flow connections
  +-- runs after all features are done

Wave 4: Audit (N agents, parallel)
  +-- each audits a section of the app
  +-- writes findings to Obsidian

Wave 5: Fixes (N agents, parallel)
  +-- each fixes issues from the audit
  +-- scoped by the audit findings
```

---

## Common Agent Failures and Prevention

| Failure | Cause | Prevention |
|---------|-------|------------|
| Agent overwrites another's work | Overlapping file ownership | Strict file ownership, no overlap |
| Agent makes locally-correct but globally-wrong change | No flow context | Give agents flow ownership, not file lists |
| Agent finishes without writing results | Forgot the instruction | ALWAYS include "write results to Obsidian" in prompt |
| Agent runs out of context | Too many tasks in one scope | Split into smaller scopes (max ~8 tasks per agent) |
| Agent uses wrong patterns | No design system reference | Point agent to Design System doc in Obsidian |
| Foundation changes conflict with parallel agents | No foundation-first step | Always run foundation alone before parallel wave |
| Agent creates new files instead of editing existing | No awareness of existing code | Tell agent to search for existing code first |
