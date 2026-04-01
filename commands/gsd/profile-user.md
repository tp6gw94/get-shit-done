---
name: gsd:profile-user
description: Generate developer behavioral profile and create Claude-discoverable artifacts
argument-hint: "[--questionnaire] [--refresh]"
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
Generate a developer behavioral profile from session analysis (or questionnaire) and produce artifacts (USER-PROFILE.md, /gsd:dev-preferences, CLAUDE.md section) that personalize Claude's responses.

Routes to the profile-user workflow which orchestrates the full flow: consent gate, session analysis or questionnaire fallback, profile generation, result display, and artifact selection.
</objective>

<available_agent_types>
Valid GSD subagent types (use exact names — do not fall back to 'general-purpose'):
- gsd-user-profiler — Analyzes developer sessions and generates behavioral profiles
</available_agent_types>

<execution_context>
@~/.claude/get-shit-done/workflows/profile-user.md
@~/.claude/get-shit-done/references/ui-brand.md
</execution_context>

<context>
Flags from $ARGUMENTS:
- `--questionnaire` -- Skip session analysis entirely, use questionnaire-only path
- `--refresh` -- Rebuild profile even when one exists, backup old profile, show dimension diff
</context>

<process>
## Orchestrator Steps (do these yourself)
1. Initialize context and check for existing profile
2. Parse $ARGUMENTS for flags (--questionnaire, --refresh)
3. Run consent gate before session analysis

## DELEGATE — Spawn gsd-user-profiler
4. Spawn gsd-user-profiler with session data or questionnaire mode
   - Pass session logs, existing profile (if --refresh), and mode flags
   - Profiler handles all analysis, scoring, and profile generation
   - Do NOT analyze sessions or generate profiles yourself

## Orchestrator Steps (do these yourself)
5. Collect profiler results (USER-PROFILE.md)
6. Display report card and highlights to user
7. Offer artifact selection (dev-preferences, CLAUDE.md sections)
8. If user selects artifacts: spawn gsd-user-profiler again for artifact generation
</process>
