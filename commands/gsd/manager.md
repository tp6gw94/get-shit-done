---
name: gsd:manager
description: Interactive command center for managing multiple phases from one terminal
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
Single-terminal command center for managing a milestone. Shows a dashboard of all phases with visual status indicators, recommends optimal next actions, and dispatches work — discuss runs inline, plan/execute run as background agents.

Designed for power users who want to parallelize work across phases from one terminal: discuss a phase while another plans or executes in the background.

**Creates/Updates:**
- No files created directly — dispatches to existing GSD commands via Skill() and background Task agents.
- Reads `.planning/STATE.md`, `.planning/ROADMAP.md`, phase directories for status.

**After:** User exits when done managing, or all phases complete and milestone lifecycle is suggested.
</objective>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-planner — Creates implementation plans for phases
- gsd-executor — Executes plans (code, tests, commits)
- gsd-phase-researcher — Researches phase requirements
- gsd-verifier — Validates completed phases
</available_agent_types>

<execution_context>
@~/.claude/get-shit-done/workflows/manager.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
No arguments required. Requires an active milestone with ROADMAP.md and STATE.md.

Project context, phase list, dependencies, and recommendations are resolved inside the workflow using `gsd-tools.cjs init manager`. No upfront context loading needed.
</context>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init manager`
2. Build and display phase dashboard with status indicators
3. Recommend optimal next actions based on phase states

## DELEGATE — Dispatch work via subagents
4. When user selects an action, spawn the appropriate subagent:
   - "Plan phase X" → invoke plan-phase skill (includes researcher + planner + checker pipeline)
   - "Execute phase X" → invoke execute-phase skill (includes executor + verifier pipeline)
   - "Verify phase X" → invoke verify-work skill (spawns gsd-verifier)
   - "Research phase X" → spawn gsd-phase-researcher
   - Discuss runs inline (lightweight, orchestrator handles)
5. Collect subagent results, refresh dashboard

## Orchestrator Steps (do these yourself)
6. Loop: display updated dashboard → recommend next → dispatch
7. Exit when user is done or all phases complete
</process>
