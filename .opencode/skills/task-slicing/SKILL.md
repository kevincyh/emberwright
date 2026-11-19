---
name: Task slicing
description: For delegators. Split a feature or story into Gemma-sized tasks with exact files, tests, skills and difficulty. Load when planning a feature.
---
# Task slicing

Adapted from the pstack `architect`, `feature` and `multi-phase-plan` playbooks and `guard-the-context-window` (MIT, Lauren Tan).

Gemma 4 is a small model with about 64k tokens of context. A task it can finish in one session:
- touches **1 to 3 files** with exact paths
- has **2 to 6 acceptance tests**, each with concrete input and expected values
- names the **skills and lesson IDs** to load
- needs no design decisions; you make them

## Steps

1. **Trace** the systems the feature touches (docs/ARCHITECTURE.md, the existing code).
2. **Shape first.** Write the Core types and signatures, say who owns each piece of state, and pick the state machine or table that replaces branching. Put this sketch into the first task.
3. **Order** the tasks so each one ends green and unblocks the next:
   1. Core types and tests
   2. Core logic and tests
   3. Data (ScriptableObjects to Core)
   4. Net and Host (thin, plus an authority test)
   5. Client (view and input)
4. **Mark dependencies** ("depends on #12"). Tasks that touch separate files may run in any order.
5. **Rate the difficulty:**
   - `low`: pure Core, with a clear formula.
   - `medium`: crosses one boundary (Data↔Core, Net↔Core).
   - `high`: concurrency, netcode authority, LAN discovery, or save migrations.

## Output format (one block per task, which becomes an issue)

```markdown
### [Task] <title>
- Parent: <story ID>
- Difficulty: low|medium|high
- Depends on: <IDs or none>
- Files: `Assets/_Game/Scripts/Core/Items/UpgradeCostCurve.cs`, `Tests/Emberwright.Core.Tests/Items/UpgradeCostCurveTests.cs`
- Skills: emberwright-tdd, core-dotnet-tests
- Lessons: 003
- Shape: `public sealed class UpgradeCostCurve { public UpgradeCostCurve(int baseCost, double growth); public int CostFor(int level); }`
- Acceptance tests:
  - `CostFor_Level1_ReturnsBaseTimesGrowth`: base 100, growth 1.5 → 150
  - `CostFor_LevelBelowOne_Throws`: level 0 → ArgumentOutOfRangeException
- Out of scope: UI, persistence
```
