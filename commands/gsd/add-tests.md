---
name: gsd:add-tests
description: Generate tests for a completed phase based on UAT criteria and implementation
argument-hint: "<phase> [additional instructions]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
argument-instructions: |
  Parse the argument as a phase number (integer, decimal, or letter-suffix), plus optional free-text instructions.
  Example: /gsd:add-tests 12
  Example: /gsd:add-tests 12 focus on edge cases in the pricing module
---
<objective>
Generate unit and E2E tests for a completed phase, using its SUMMARY.md, CONTEXT.md, and VERIFICATION.md as specifications.

Analyzes implementation files, classifies them into TDD (unit), E2E (browser), or Skip categories, presents a test plan for user approval, then generates tests following RED-GREEN conventions.

Output: Test files committed with message `test(phase-{N}): add unit and E2E tests from add-tests command`
</objective>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-executor — Generates and runs tests based on phase specifications
</available_agent_types>

<execution_context>
@~/.claude/get-shit-done/workflows/add-tests.md
</execution_context>

<context>
Phase: $ARGUMENTS

@.planning/STATE.md
@.planning/ROADMAP.md
</context>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init add-tests`
2. Parse $ARGUMENTS for phase number and additional instructions
3. Gather phase artifacts: SUMMARY.md, CONTEXT.md, VERIFICATION.md

## DELEGATE — Spawn gsd-executor
4. Spawn gsd-executor with test generation context
   - Pass phase artifacts, implementation file list, workflow from @~/.claude/get-shit-done/workflows/add-tests.md
   - Executor handles file classification, test plan creation, RED-GREEN test generation, and commits
   - Do NOT classify files or write tests yourself

## Orchestrator Steps (do these yourself)
5. Collect executor results (test files created, coverage summary)
6. Present results and any gap reports to user
</process>
