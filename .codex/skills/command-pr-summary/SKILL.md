---
name: command-pr-summary
description: |
  Implements the `/pr-summary` slash-style command for Codex. Use when the user types `/pr-summary`, asks to run the `pr-summary` command, or wants the AI Development Framework workflow: Generate PR summary from current branch.
---

# /pr-summary Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/pr-summary` as a command invocation, not as ordinary prose.
- If the user provides text after `/pr-summary`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

Generate a comprehensive pull request summary from the data below.

## Live Branch Data

### Commits on this branch
!`~/.codex/hooks/speckit-helper.sh pr-commits`

### Files changed
!`~/.codex/hooks/speckit-helper.sh pr-files`

### Diff stats
!`~/.codex/hooks/speckit-helper.sh pr-stats`

## Output Format

```markdown
## Summary
[1-3 bullet points describing the change]

## Changes
- feat: [feature description]
- fix: [fix description]

## Files Changed
- `path/to/file` - [change description]

## Breaking Changes
- [None / list of breaking changes]

## Test Plan
- [ ] [Test scenario 1]
- [ ] [Test scenario 2]

Generated with Codex
```
