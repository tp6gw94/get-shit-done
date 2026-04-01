---
name: gsd:execute-phase
description: Execute all plans in a phase with wave-based parallelization
argument-hint: "<phase-number> [--wave N] [--gaps-only] [--interactive]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
  - Task
  - TodoWrite
  - AskUserQuestion
---
<objective>
Execute all plans in a phase using wave-based parallel execution.

Orchestrator stays lean: discover plans, analyze dependencies, group into waves, spawn subagents, collect results. Each subagent loads the full execute-plan context and handles its own plan.

Optional wave filter:
- `--wave N` executes only Wave `N` for pacing, quota management, or staged rollout
- phase verification/completion still only happens when no incomplete plans remain after the selected wave finishes

Flag handling rule:
- The optional flags documented below are available behaviors, not implied active behaviors
- A flag is active only when its literal token appears in `$ARGUMENTS`
- If a documented flag is absent from `$ARGUMENTS`, treat it as inactive

Context budget: ~15% orchestrator, 100% fresh per subagent.
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/execute-phase.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
Phase: $ARGUMENTS

**Available optional flags (documentation only — not automatically active):**
- `--wave N` — Execute only Wave `N` in the phase. Use when you want to pace execution or stay inside usage limits.
- `--gaps-only` — Execute only gap closure plans (plans with `gap_closure: true` in frontmatter). Use after verify-work creates fix plans.
- `--interactive` — Execute plans sequentially inline (no subagents) with user checkpoints between tasks. Lower token usage, pair-programming style. Best for small phases, bug fixes, and verification gaps.

**Active flags must be derived from `$ARGUMENTS`:**
- `--wave N` is active only if the literal `--wave` token is present in `$ARGUMENTS`
- `--gaps-only` is active only if the literal `--gaps-only` token is present in `$ARGUMENTS`
- `--interactive` is active only if the literal `--interactive` token is present in `$ARGUMENTS`
- If none of these tokens appear, run the standard full-phase execution flow with no flag-specific filtering
- Do not infer that a flag is active just because it is documented in this prompt

Context files are resolved inside the workflow via `gsd-tools init execute-phase` and per-subagent `<files_to_read>` blocks.
</context>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-executor — Executes implementation plans (code, tests, commits)
- gsd-verifier — Validates built features against specs
</available_agent_types>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init execute-phase`
2. Parse $ARGUMENTS for phase number and flags (--wave, --gaps-only, --interactive)
3. Discover plans in the phase directory, analyze dependencies, group into waves

## DELEGATE — Spawn gsd-executor subagents
4. For each wave, spawn gsd-executor subagent(s) for each plan (up to 4 in parallel)
   - Pass the full plan path, workflow context from @~/.claude/get-shit-done/workflows/execute-phase.md, and any relevant <files_to_read> blocks
   - Each executor gets a fresh context window — do NOT execute plans yourself
5. Collect results from each executor, update STATE.md via gsd-tools

## DELEGATE — Spawn gsd-verifier (after all waves complete)
6. If verification is needed (no --interactive flag), spawn gsd-verifier to validate the phase
   - Pass phase number, SUMMARY.md paths, and verification criteria

## Orchestrator Steps (do these yourself)
7. Update ROADMAP.md progress via gsd-tools
8. Present results to user, offer next actions

**Exception:** If `--interactive` flag is active, execute plans sequentially inline (no subagents) as documented in the workflow.
</process>
