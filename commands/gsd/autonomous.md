---
name: gsd:autonomous
description: Run all remaining phases autonomously — discuss→plan→execute per phase
argument-hint: "[--from N]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
  - Task
---
<objective>
Execute all remaining milestone phases autonomously. For each phase: discuss → plan → execute. Pauses only for user decisions (grey area acceptance, blockers, validation requests).

Uses ROADMAP.md phase discovery and Skill() flat invocations for each phase command. After all phases complete: milestone audit → complete → cleanup.

**Creates/Updates:**
- `.planning/STATE.md` — updated after each phase
- `.planning/ROADMAP.md` — progress updated after each phase
- Phase artifacts — CONTEXT.md, PLANs, SUMMARYs per phase

**After:** Milestone is complete and cleaned up.
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/autonomous.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
Optional flag: `--from N` — start from phase N instead of the first incomplete phase.

Project context, phase list, and state are resolved inside the workflow using init commands (`gsd-tools.cjs init milestone-op`, `gsd-tools.cjs roadmap analyze`). No upfront context loading needed.
</context>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-phase-researcher — Researches domain context for each phase
- gsd-planner — Creates implementation plans for each phase
- gsd-plan-checker — Reviews plans for quality
- gsd-executor — Executes plans (code, tests, commits)
- gsd-verifier — Validates completed phases
- gsd-roadmapper — Creates/updates project roadmaps
- gsd-integration-checker — Checks cross-phase integration
</available_agent_types>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init milestone-op`
2. Discover remaining phases from ROADMAP.md
3. Parse $ARGUMENTS for --from N flag

## Per-Phase Loop (DELEGATE each step)
For each remaining phase:

### Orchestrator Step — Discuss (inline)
4a. Run lightweight discussion to gather phase context (or skip if CONTEXT.md exists)

### DELEGATE — Spawn planning agents
4b. Spawn gsd-phase-researcher → collect RESEARCH.md
4c. Spawn gsd-planner → collect PLAN.md files
4d. Spawn gsd-plan-checker → verify plans

### DELEGATE — Spawn execution agents
4e. Spawn gsd-executor(s) for each plan (up to 4 in parallel per wave)
4f. Spawn gsd-verifier to validate phase

### Orchestrator Steps
4g. Update STATE.md and ROADMAP.md progress
4h. Check for blockers — pause for user if needed

## After All Phases Complete
5. Spawn gsd-integration-checker for cross-phase audit
6. Run milestone completion flow
7. Present final summary to user
</process>
