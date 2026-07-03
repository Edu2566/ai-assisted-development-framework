---
name: command-speckit-implement
description: |
  Implements the `/speckit.implement` slash-style command for Codex. Use when the user types `/speckit.implement`, asks to run the `speckit.implement` command, or wants the AI Development Framework workflow: Execute TDD implementation from spec-kit artifacts with quality gates.
---

# /speckit.implement Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/speckit.implement` as a command invocation, not as ordinary prose.
- If the user provides text after `/speckit.implement`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

# Spec-Kit: Implement

Execute the implementation plan using strict TDD cycles with quality gates.

## Pre-Flight

### Current branch
!`~/.codex/hooks/speckit-helper.sh branch`

### Load artifacts
!`~/.codex/hooks/speckit-helper.sh check-artifacts`

### Constitution
!`~/.codex/hooks/speckit-helper.sh constitution`

### Checklists
!`~/.codex/hooks/speckit-helper.sh checklists`

### Test framework detection
!`~/.codex/hooks/speckit-helper.sh detect-test-framework`

## Instructions

**Required inputs**: `tasks.md`, `plan.md`, and `spec.md` must all exist. If any are missing, tell the user which command to run first.

### Pre-Implementation Gate

1. **Checklist validation**: if checklists exist in `.specify/specs/<branch>/checklists/`, scan for unchecked items:
   - If CRITICAL unchecked items exist → **pause and ask user** whether to proceed or resolve first
   - If only MEDIUM/LOW unchecked items → warn but continue

2. **Load all artifacts**:
   - `tasks.md` — the task list to execute
   - `plan.md` — design decisions and affected files
   - `spec.md` — requirements and success criteria
   - `constitution.md` — governance principles
   - `data-model.md`, `contracts/` — if they exist

### Phase-by-Phase TDD Execution

3. **For each task in tasks.md** (in order, respecting phase boundaries):

   **a. Mark task in progress**
   - Update `tasks.md`: change `- [ ]` to `- [~]` for current task
   - Mark the matching Codex plan item `in_progress` when running interactively

   **b. Write failing test** (Red phase)
   - Use `test-specialist` agent patterns to identify test location and conventions
   - Write a test that validates the task's acceptance criteria
   - Test MUST fail at this point (implementation doesn't exist yet)
   - Run the test to confirm failure

   **c. Verify failure**
   - Execute the specific test
   - Confirm it fails for the expected reason (not a syntax error or import issue)
   - If it passes unexpectedly, the test isn't testing new behavior — revise it

   **d. Implement** (Green phase)
   - Write minimum code to make the failing test pass
   - Follow code quality limits from `.codex/rules/code-quality.md`:
     - Functions < 50 lines
     - Files < 500 lines
     - Cyclomatic complexity < 10
   - Follow patterns identified in `plan.md`

   **e. Verify pass**
   - Run the specific test — it must now pass
   - Run the full test suite — no regressions allowed
   - If any test fails, fix the implementation (never modify the test to make it pass)

   **f. Mark task complete**
   - Update `tasks.md`: change `- [~]` to `- [x]`
   - Mark the matching Codex plan item `completed` when running interactively

4. **Between phases**: run quality checks using `quality-guardian` patterns:
   - Linting, type checking, formatting
   - Full test suite
   - Fix any issues before proceeding to the next phase

### Completion

5. **Final quality gate**:
   - Run all quality checks (lint, types, format, tests)
   - Verify all tasks in `tasks.md` are `[x]`
   - Verify all Codex plan items are `completed`

6. **Generate completion report**:
   ```
   IMPLEMENTATION REPORT
   =====================
   Tasks completed: X/Y
   Tests written:   N
   Tests passing:   N/N
   Quality checks:  PASS/FAIL
   Files modified:  N
   Files created:   N

   Coverage mapping:
   FR-001 → T001, T002 → PASS
   FR-002 → T003       → PASS
   SC-001 → verified via T001

   Constitution compliance: ALL PRINCIPLES MET
   ```

7. **Suggest next steps**:
   - `/speckit.analyze` for cross-artifact consistency check
   - Commit and create PR when ready
