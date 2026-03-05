# Example: Real-World Production App

A real-world example of infinite-context on a 687-user-story React Native app for enterprise dealer management.

## Project Stats
- **Tech**: Expo Router + Zustand + TanStack Query + Drizzle ORM
- **Scale**: 180+ screens, 9 stakeholder roles, 4 modules
- **Build**: 9 waves using parallel agents, tracked via Obsidian
- **Audit**: 6 parallel audit agents found 102 issues
- **Fix**: 13 agents across 5 fix waves resolved 64 issues in ~66 minutes

## How Obsidian Was Used

### During Build (9 waves)
```
Obsidian Vault:
  MyProject/
    Architecture.md           <- code structure, stores, routing
    Design System.md          <- colors, fonts, spacing rules
    Build/
      Build Checklist.md      <- 257 deliverables tracked
      Wave 1/
        README.md             <- wave overview
        Agent A.md            <- scope: critical fixes
        Agent B.md            <- scope: database schema
        Agent C.md            <- scope: sales components
        Agent D.md            <- scope: form components
      Wave 2/ ... Wave 9/
```

Each wave had 2-6 agents running in parallel. Agents read scopes from Obsidian, worked on non-overlapping files, wrote results back.

### During Audit
```
Obsidian Vault:
  MyProject/
    Build/
      Audit Wave/
        Agent AUD1.md         <- scope: sales flow audit
        AUD1 Results.md       <- findings: 20 screens rated
        Agent AUD2.md         <- scope: business logic audit
        AUD2 Results.md       <- findings
        ...AUD3-AUD6...
        Master Gap Report.md  <- compiled: 102 issues found
```

6 audit agents ran in parallel, each covering a different section of the app.

### During Fix Waves
```
Obsidian Vault:
  MyProject/
    Build/
      Fix Waves/
        Foundation Agent.md   <- scope: shared data fixes (ran alone first)
        Foundation Results.md
        FA1 Results.md        <- customer auth fix
        FA2 Results.md        <- data isolation fix
        FA3 Results.md        <- data cleanup
        FA4 Results.md        <- missing hook fix
        FB1-FB3 Results.md    <- role isolation fixes
        FC1-FC3 Results.md    <- core logic fixes
        FD1-FD2 Results.md    <- data + content fixes
        Final Report.md       <- summary: 64 issues, 0 TS errors
```

### MEMORY.md (what was loaded every message)
```markdown
# MyProject -- Memory

## Project
- Expo Router React Native app for enterprise dealer management
- 687 user stories across 4 modules (Admin, Sales, Inventory, Service)

## Architecture (pointer)
- Full docs -> Obsidian: `MyProject/Architecture.md`
- Stores: auth-store, leads-store, wizard-store, filter-store, customer-store
- Routing: Expo Router file-based

## Design System (pointer)
- Full docs -> Obsidian: `MyProject/Design System.md`
- Light theme, Geist fonts, borderRadius 16

## Gotchas
- zsh: always quote paths with parens: "app/(tabs)/file.tsx"
- TypeScript: npx tsc --noEmit --skipLibCheck
- Mock data: production-realistic with local currency pricing
```

~20 lines. Full context accessible via Obsidian pointers. Loaded cheaply every message.

### CLAUDE.md (what was in the repo)
```markdown
# MyProject -- Claude Project Instructions

## Design System (summary only -- full in Obsidian)
Colors: background=#F6F6F8, surface=#FFFFFF, textPrimary=#1A1A1A, accent=#1A1A1A
Fonts: Geist_600SemiBold (headings), Geist_400Regular (body), GeistMono_500Medium (numbers)

## Demo Credentials
| Role | ID | Password |
|------|------------|----------|
| Sales Consultant | EMP-2847 | dse123 |
| Team Lead | EMP-1234 | tl123 |
| Manager | EMP-0001 | sm123 |

## Shell Gotchas
- zsh glob: always quote paths with parens
- TypeScript check: npx tsc --noEmit --skipLibCheck
```

~30 lines. Just enough for every session. Everything else in Obsidian.

## Results

| Metric | Value |
|--------|-------|
| Build waves completed | 9 |
| Deliverables built | 101 |
| Audit issues found | 102 |
| Issues fixed | 64 |
| Fix agents used | 13 |
| TypeScript errors after fixes | 0 |
| File conflicts between agents | 0 |
| Total fix time | ~66 minutes |
