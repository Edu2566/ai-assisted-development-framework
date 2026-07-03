---
name: command-context
description: |
  Implements the `/context` slash-style command for Codex. Use when the user types `/context`, asks to run the `context` command, or wants the AI Development Framework workflow: Refresh project context analysis.
---

# /context Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/context` as a command invocation, not as ordinary prose.
- If the user provides text after `/context`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

Perform a comprehensive project context analysis using the `context-analysis` skill methodology plus the live data below.

## Live Project Data

### Recent commits
!`~/.codex/hooks/speckit-helper.sh recent-commits`

### Active branch
!`~/.codex/hooks/speckit-helper.sh branch`

### Directory structure (top 2 levels)
!`~/.codex/hooks/speckit-helper.sh project-files`

## Output Format

```
PROJECT CONTEXT
===============
Tech Stack: [languages, frameworks, versions]
Architecture: [pattern identified]
Testing: [framework, test command]
Quality Tools: [linters, formatters, type checkers with commands]
Build/Dev: [build system, dev server, CI/CD]
Key Dependencies: [list with purposes]
Entry Points: [main files/scripts]
Recent Activity: [active branches, recent changes]
Recommendations: [approach for upcoming work]
```
