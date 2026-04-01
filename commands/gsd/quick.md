---
name: gsd:quick
description: Execute a quick task with GSD guarantees (atomic commits, state tracking) but skip optional agents
argument-hint: "[--full] [--discuss] [--research]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - Task
  - AskUserQuestion
---
<objective>
Execute small, ad-hoc tasks with GSD guarantees (atomic commits, STATE.md tracking).

Quick mode is the same system with a shorter path:
- Spawns gsd-planner (quick mode) + gsd-executor(s)
- Quick tasks live in `.planning/quick/` separate from planned phases
- Updates STATE.md "Quick Tasks Completed" table (NOT ROADMAP.md)

**Default:** Skips research, discussion, plan-checker, verifier. Use when you know exactly what to do.

**`--discuss` flag:** Lightweight discussion phase before planning. Surfaces assumptions, clarifies gray areas, captures decisions in CONTEXT.md. Use when the task has ambiguity worth resolving upfront.

**`--full` flag:** Enables plan-checking (max 2 iterations) and post-execution verification. Use when you want quality guarantees without full milestone ceremony.

**`--research` flag:** Spawns a focused research agent before planning. Investigates implementation approaches, library options, and pitfalls for the task. Use when you're unsure of the best approach.

Flags are composable: `--discuss --research --full` gives discussion + research + plan-checking + verification.
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/quick.md
</execution_context>

<context>
$ARGUMENTS

Context files are resolved inside the workflow (`init quick`) and delegated via `<files_to_read>` blocks.
</context>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-planner — Creates the quick task plan
- gsd-executor — Executes the quick task plan
- gsd-phase-researcher — Researches implementation approaches (only with --research flag)
- gsd-plan-checker — Reviews plan quality (only with --full flag)
- gsd-verifier — Validates execution results (only with --full flag)
</available_agent_types>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init quick`
2. Parse $ARGUMENTS for task description and flags (--full, --discuss, --research)
3. If --discuss: run lightweight discussion inline to capture decisions in CONTEXT.md

## DELEGATE — Spawn gsd-phase-researcher (only with --research flag)
4. If --research active: spawn gsd-phase-researcher for focused research
   - Collect findings for planner context

## DELEGATE — Spawn gsd-planner (quick mode)
5. Spawn gsd-planner with task description and any research/discussion context
   - Planner creates PLAN.md in `.planning/quick/`

## DELEGATE — Spawn gsd-plan-checker (only with --full flag)
6. If --full active: spawn gsd-plan-checker to verify plan quality
   - If issues: re-spawn gsd-planner with feedback (max 2 iterations)

## DELEGATE — Spawn gsd-executor
7. Spawn gsd-executor with the PLAN.md
   - Executor handles code, tests, commits

## DELEGATE — Spawn gsd-verifier (only with --full flag)
8. If --full active: spawn gsd-verifier to validate results

## Orchestrator Steps (do these yourself)
9. Update STATE.md quick tasks table via gsd-tools
10. Present results to user
</process>
