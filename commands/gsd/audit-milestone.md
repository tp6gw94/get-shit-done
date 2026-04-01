---
name: gsd:audit-milestone
description: Audit milestone completion against original intent before archiving
argument-hint: "[version]"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Task
  - Write
---
<objective>
Verify milestone achieved its definition of done. Check requirements coverage, cross-phase integration, and end-to-end flows.

**This command IS the orchestrator.** Reads existing VERIFICATION.md files (phases already verified during execute-phase), aggregates tech debt and deferred gaps, then spawns integration checker for cross-phase wiring.
</objective>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-integration-checker — Checks cross-phase integration and wiring
</available_agent_types>

<execution_context>
@~/.claude/get-shit-done/workflows/audit-milestone.md
</execution_context>

<context>
Version: $ARGUMENTS (optional — defaults to current milestone)

Core planning files are resolved in-workflow (`init milestone-op`) and loaded only as needed.

**Completed Work:**
Glob: .planning/phases/*/*-SUMMARY.md
Glob: .planning/phases/*/*-VERIFICATION.md
</context>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context via `gsd-tools.cjs init milestone-op`
2. Read existing VERIFICATION.md and SUMMARY.md files from all phases
3. Aggregate tech debt and deferred gaps
4. Determine scope of integration check needed

## DELEGATE — Spawn gsd-integration-checker
5. Spawn gsd-integration-checker for cross-phase wiring validation
   - Pass phase summaries, verification results, and integration points
   - Do NOT perform integration checks yourself

## Orchestrator Steps (do these yourself)
6. Collect integration check results
7. Compile final audit report (requirements coverage, tech debt, gaps)
8. Present results and recommend next steps (complete milestone or fix gaps)
</process>
