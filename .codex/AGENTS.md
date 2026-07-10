# AI Development Framework v4.3

## Custom Agents

| Agent | Role | When to Use |
|-------|------|-------------|
| **test-specialist** | Testing | After implementation, comprehensive tests |
| **quality-guardian** | QA | Before any commit, PR, or merge |
| **code-reviewer** | Code review | Before PR creation, spec compliance + quality |
| **review-coordinator** | PR management | Creating PRs, managing review workflows |
| **forensic-specialist** | Security | Security audits, suspicious patterns |

For general tasks, use Codex's built-in repository exploration, planning, implementation, and review tools before reaching for specialized framework agents.

## Task Management
- Use Codex plan tracking for any task with >2 steps.
- Keep exactly one step `in_progress` at a time.
- Mark steps `completed` immediately after finishing them.
- Use subagents only for bounded work that can run independently or in parallel.

## Core Rules
1. Do what has been asked; nothing more, nothing less
2. NEVER create files unless absolutely necessary for achieving the goal
3. ALWAYS prefer editing existing files over creating new ones
4. NEVER proactively create documentation files (*.md) unless explicitly requested
5. Only commit when explicitly requested by the user
6. Follow existing project patterns rather than imposing new conventions
7. Keep responses concise and focused on the task
8. Use `git -C <directory>` instead of `cd <directory> && git` to avoid zoxide conflicts
9. Version declaration is deprecated in docker compose, do not add it

## Performance & Model Selection
- Default reasoning effort is configured in `.codex/config.toml`.
- Use lower reasoning for quick exploration and higher reasoning for architecture, security, and multi-file refactors.
- Subagents inherit the current model unless the user explicitly requests a different model or there is a clear task-specific reason.
- Use rtk when available to compress verbose command output before it reaches the main context.

## Multi-Environment Workflows
- Use `codex resume` to continue local CLI sessions.
- Use Codex Cloud/App surfaces for hosted tasks where available, and apply changes locally when appropriate.
- For long-running work, use the Document & Clear pattern in `.codex/progress.md` or a project-specific progress file.

## Tool Usage
- Read existing code before suggesting modifications.
- Use `rg`/file search to find similar implementations.
- Use shell commands only for system commands and terminal operations.
- For complex features, plan first and get user approval before broad implementation.
- For slash-style commands, use the mirrored `command-*` skills. Examples: `/map-context` -> `command-map-context`, `/quality` -> `command-quality`, `/speckit.plan` -> `command-speckit-plan`.
- For spec-driven development (SDD), use the command skills for: init, brainstorm, specify, plan, review, tasks, implement.
- For trivial changes (typos, config), use the `speckit.fix` recipe to bypass the full pipeline.
- For brownfield projects, use the `speckit.baseline` recipe to reverse-engineer specs from existing code.

See `.codex/rules/` for detailed policies on code quality, git workflow, agent coordination, and language-specific tooling.
