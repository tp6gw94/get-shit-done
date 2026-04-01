---
name: gsd:verify-work
description: Validate built features through conversational UAT
argument-hint: "[phase number, e.g., '4']"
allowed-tools:
  - Read
  - Bash
  - Glob
  - Grep
  - Edit
  - Write
  - Task
---
<objective>
Validate built features through conversational testing with persistent state.

Purpose: Confirm what Claude built actually works from user's perspective. One test at a time, plain text responses, no interrogation. When issues are found, automatically diagnose, plan fixes, and prepare for execution.

Output: {phase_num}-UAT.md tracking all test results. If issues found: diagnosed gaps, verified fix plans ready for /gsd:execute-phase
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/verify-work.md
@~/.claude/get-shit-done/templates/UAT.md
</execution_context>

<context>
Phase: $ARGUMENTS (optional)
- If provided: Test specific phase (e.g., "4")
- If not provided: Check for active sessions or prompt for phase

Context files are resolved inside the workflow (`init verify-work`) and delegated via `<files_to_read>` blocks.
</context>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-verifier — Validates built features against specs and runs UAT
</available_agent_types>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init verify-work`
2. Parse $ARGUMENTS for phase number
3. Check for active verification sessions

## DELEGATE — Spawn gsd-verifier
4. Spawn gsd-verifier with phase context
   - Pass phase number, SUMMARY.md paths, VERIFICATION.md template, UAT criteria
   - The verifier handles all testing, diagnosis, and gap identification
   - Do NOT run tests or verify work yourself

## Orchestrator Steps (do these yourself)
5. Collect verifier results (UAT.md, any gap plans)
6. If issues found: present diagnosed gaps and fix plans to user
7. Offer to run `/gsd:execute-phase --gaps-only` for fixes
</process>
