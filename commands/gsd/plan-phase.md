---
name: gsd:plan-phase
description: Create detailed phase plan (PLAN.md) with verification loop
argument-hint: "[phase] [--auto] [--research] [--skip-research] [--gaps] [--skip-verify] [--prd <file>] [--reviews] [--text]"
agent: gsd-planner
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Task
  - WebFetch
  - mcp__context7__*
---
<objective>
Create executable phase prompts (PLAN.md files) for a roadmap phase with integrated research and verification.

**Default flow:** Research (if needed) → Plan → Verify → Done

**Orchestrator role:** Parse arguments, validate phase, research domain (unless skipped), spawn gsd-planner, verify with gsd-plan-checker, iterate until pass or max iterations, present results.
</objective>

<execution_context>
@~/.claude/get-shit-done/workflows/plan-phase.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
Phase number: $ARGUMENTS (optional — auto-detects next unplanned phase if omitted)

**Flags:**
- `--research` — Force re-research even if RESEARCH.md exists
- `--skip-research` — Skip research, go straight to planning
- `--gaps` — Gap closure mode (reads VERIFICATION.md, skips research)
- `--skip-verify` — Skip verification loop
- `--prd <file>` — Use a PRD/acceptance criteria file instead of discuss-phase. Parses requirements into CONTEXT.md automatically. Skips discuss-phase entirely.
- `--reviews` — Replan incorporating cross-AI review feedback from REVIEWS.md (produced by `/gsd:review`)
- `--text` — Use plain-text numbered lists instead of TUI menus (required for `/rc` remote sessions)

Normalize phase input in step 2 before any directory lookups.
</context>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-phase-researcher — Researches domain context for a phase
- gsd-planner — Creates detailed implementation plans (PLAN.md files)
- gsd-plan-checker — Reviews plans for quality and completeness
</available_agent_types>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init plan-phase`
2. Parse $ARGUMENTS for phase number and flags
3. Validate phase exists in ROADMAP.md, check for existing plans

## DELEGATE — Spawn gsd-phase-researcher (unless --skip-research)
4. If research needed: spawn gsd-phase-researcher with phase context
   - Pass ROADMAP.md phase description, CONTEXT.md if exists, any --prd file
   - Collect RESEARCH.md output

## DELEGATE — Spawn gsd-planner
5. Spawn gsd-planner with all gathered context
   - Pass phase info, RESEARCH.md (if produced), CONTEXT.md, workflow from @~/.claude/get-shit-done/workflows/plan-phase.md
   - Collect PLAN.md files

## DELEGATE — Spawn gsd-plan-checker (unless --skip-verify)
6. Spawn gsd-plan-checker to verify plan quality
   - Pass PLAN.md files and phase requirements
   - If checker returns issues: re-spawn gsd-planner with feedback (max 2 iterations)

## Orchestrator Steps (do these yourself)
7. Present plan summary to user
8. Update STATE.md via gsd-tools
</process>
