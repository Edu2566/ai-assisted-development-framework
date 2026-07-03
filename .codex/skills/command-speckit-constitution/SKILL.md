---
name: command-speckit-constitution
description: |
  Implements the `/speckit.constitution` slash-style command for Codex. Use when the user types `/speckit.constitution`, asks to run the `speckit.constitution` command, or wants the AI Development Framework workflow: Create or update .specify/memory/constitution.md with project governance principles.
---

# /speckit.constitution Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/speckit.constitution` as a command invocation, not as ordinary prose.
- If the user provides text after `/speckit.constitution`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

# Spec-Kit: Constitution

Create or update the project constitution at `.specify/memory/constitution.md`.

## Pre-Flight

### Existing constitution
!`~/.codex/hooks/speckit-helper.sh constitution`

### Project context
!`~/.codex/hooks/speckit-helper.sh readme-head`

### Tech stack detection
!`~/.codex/hooks/speckit-helper.sh list-config-files`

### Existing rules
!`~/.codex/hooks/speckit-helper.sh list-rules`

## Instructions

1. **Analyze project context** from the pre-flight data above:
   - Identify language, framework, architecture patterns
   - Read config files to understand dependencies and tooling
   - Check `.codex/rules/` to understand existing conventions (reference them, don't duplicate)

2. **Propose 5 principles** — these should cover project-specific governance that complements `.codex/rules/`:
   - **Tech stack decisions** (e.g., "Use PostgreSQL for all persistent storage")
   - **Architecture constraints** (e.g., "All API endpoints must be REST, no GraphQL")
   - **Code organization** (e.g., "Feature-based directory structure")
   - **Testing philosophy** (e.g., "Integration tests over unit tests for API layer")
   - **Dependency policy** (e.g., "Minimize external dependencies, prefer stdlib")

3. **Interactive confirmation** — present each principle using AskUserQuestion:
   - Show all 5 proposed principles with descriptions
   - Let user customize, reorder, or replace each one
   - Use multiSelect to let user approve multiple at once

4. **Write constitution** to `.specify/memory/constitution.md`:
   ```markdown
   # Project Constitution
   <!-- Version: 1.0.0 | Date: YYYY-MM-DD -->
   <!-- Updated by /speckit.constitution -->

   ## Principles

   1. **<Principle Name>** — <description>
   2. **<Principle Name>** — <description>
   3. **<Principle Name>** — <description>
   4. **<Principle Name>** — <description>
   5. **<Principle Name>** — <description>

   ## Tech Stack
   - Language: <detected>
   - Framework: <detected>
   - Database: <detected or N/A>
   - Testing: <detected>

   ## Architecture Constraints
   <user-confirmed constraints>

   ## References
   - Project rules: `.codex/rules/`
   - Code quality: `.codex/rules/code-quality.md`
   - Git workflow: `.codex/rules/git-workflow.md`
   ```

5. **Version management**:
   - If creating new: version `1.0.0`
   - If updating existing: bump minor version (e.g., `1.0.0` → `1.1.0`)
   - Always include current date

6. **Post-write**: suggest committing the updated constitution
