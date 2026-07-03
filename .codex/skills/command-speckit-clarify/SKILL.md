---
name: command-speckit-clarify
description: |
  Implements the `/speckit.clarify` slash-style command for Codex. Use when the user types `/speckit.clarify`, asks to run the `speckit.clarify` command, or wants the AI Development Framework workflow: Scan spec for ambiguities and ask targeted clarification questions.
---

# /speckit.clarify Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/speckit.clarify` as a command invocation, not as ordinary prose.
- If the user provides text after `/speckit.clarify`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

# Spec-Kit: Clarify

Scan the current branch's specification for ambiguities and generate targeted clarification questions.

## Pre-Flight

### Current branch
!`~/.codex/hooks/speckit-helper.sh branch`

### Load spec
!`~/.codex/hooks/speckit-helper.sh spec`

### Existing clarifications
!`~/.codex/hooks/speckit-helper.sh clarifications`

## Instructions

1. **Load and analyze spec.md** against 9 taxonomy categories:

   | # | Category | What to look for |
   |---|----------|-----------------|
   | 1 | **Scope boundaries** | What's in/out of scope for this feature |
   | 2 | **User roles** | Who are the actors, what permissions do they have |
   | 3 | **Data lifecycle** | Creation, update, deletion, retention policies |
   | 4 | **Error handling** | What happens when things go wrong |
   | 5 | **Edge cases** | Boundary conditions, empty states, limits |
   | 6 | **Performance** | Expected load, response times, concurrency |
   | 7 | **Security** | Authentication, authorization, data protection |
   | 8 | **Integration** | External dependencies, APIs, third-party services |
   | 9 | **Migration** | Backwards compatibility, data migration, rollback |

2. **Generate max 5 questions** — prioritize by impact on implementation:
   - Each question must be specific and actionable
   - Use AskUserQuestion with multiple-choice options
   - Include a recommended answer as the first option (marked with "(Recommended)")
   - Questions should resolve `[NEEDS CLARIFICATION]` markers if present

3. **Present questions one at a time** using AskUserQuestion:
   - Category tag in the header (e.g., "Error handling")
   - Clear question with context from the spec
   - 2-4 options with descriptions

4. **Update spec.md incrementally** — after each answer:
   - Add or update the `## Clarifications` section at the bottom of spec.md
   - Format:
     ```markdown
     ## Clarifications

     ### CLR-001: <question summary> [<category>]
     **Decision**: <chosen answer>
     **Rationale**: <why this was chosen>
     **Impact**: <which FR/US this affects>
     ```
   - Remove any resolved `[NEEDS CLARIFICATION]` markers from the spec body
   - Update affected functional requirements if the clarification changes them

5. **Summary** after all questions are answered:
   - List all clarifications added
   - Note any remaining `[NEEDS CLARIFICATION]` markers
   - Suggest next step: `/speckit.plan` if ready, or another `/speckit.clarify` round if more questions remain
