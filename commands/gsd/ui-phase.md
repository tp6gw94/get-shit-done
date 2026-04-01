---
name: gsd:ui-phase
description: Generate UI design contract (UI-SPEC.md) for frontend phases
argument-hint: "[phase]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Task
  - WebFetch
  - AskUserQuestion
  - mcp__context7__*
---
<objective>
Create a UI design contract (UI-SPEC.md) for a frontend phase.
Orchestrates gsd-ui-researcher and gsd-ui-checker.
Flow: Validate → Research UI → Verify UI-SPEC → Done
</objective>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-ui-researcher — Researches UI patterns, component libraries, and design systems
- gsd-ui-checker — Validates UI-SPEC.md against design standards and accessibility
</available_agent_types>

<execution_context>
@~/.claude/get-shit-done/workflows/ui-phase.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
Phase number: $ARGUMENTS — optional, auto-detects next unplanned phase if omitted.
</context>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init ui-phase`
2. Parse $ARGUMENTS for phase number
3. Validate phase exists and is a frontend phase

## DELEGATE — Spawn gsd-ui-researcher
4. Spawn gsd-ui-researcher with phase context
   - Pass ROADMAP.md phase description, CONTEXT.md, ui-brand reference
   - Researcher produces UI-SPEC.md

## DELEGATE — Spawn gsd-ui-checker
5. Spawn gsd-ui-checker to validate the UI-SPEC.md
   - Pass UI-SPEC.md and phase requirements
   - If checker returns issues: re-spawn gsd-ui-researcher with feedback

## Orchestrator Steps (do these yourself)
6. Present UI-SPEC.md summary to user
7. Update STATE.md via gsd-tools
</process>
