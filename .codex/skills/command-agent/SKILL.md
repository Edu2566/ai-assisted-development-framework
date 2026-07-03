---
name: command-agent
description: |
  Implements the `/agent` slash-style command for Codex. Use when the user types `/agent`, asks to run the `agent` command, or wants the AI Development Framework workflow: Coordinate a development task with full workflow.
---

# /agent Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/agent` as a command invocation, not as ordinary prose.
- If the user provides text after `/agent`, treat it as this command argument: `development task description`.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

Plan this development task first, then follow the development workflow in `.codex/rules/agent-workflow.md` with Codex plan tracking, quality gates, and spec task progress: $ARGUMENTS
