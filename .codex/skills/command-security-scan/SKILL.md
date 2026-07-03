---
name: command-security-scan
description: |
  Implements the `/security-scan` slash-style command for Codex. Use when the user types `/security-scan`, asks to run the `security-scan` command, or wants the AI Development Framework workflow: Quick security scan of current changes.
---

# /security-scan Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/security-scan` as a command invocation, not as ordinary prose.
- If the user provides text after `/security-scan`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

Perform a quick security review of the current changes using the `security-review` skill methodology.

## Scope
Focus on staged and unstaged changes:
1. Run `git diff --staged` and `git diff` to identify changed files
2. Apply the security-review skill checklist to those changes only

## Checklist
- [ ] No hardcoded secrets, API keys, or passwords in code
- [ ] No sensitive data in committed files
- [ ] SQL queries use parameterized statements
- [ ] User input is validated and output is encoded
- [ ] Auth/authz patterns follow existing project conventions
- [ ] New dependencies checked for known vulnerabilities

## Automated Tool Checks (if available)
- **Secrets**: `gitleaks detect --staged` — scans for 150+ secret types
- **SAST**: `semgrep scan --config auto` — cross-language pattern-based security analysis
- **SCA**: language-specific dependency scanning:
  - Go: `govulncheck ./...` (reachability-based — only flags actually-called vulnerable code)
  - JS/TS: `npm audit`
  - Python: `pip-audit`
  - Rust: `cargo audit`
  - Java: OWASP Dependency-Check

## Report Format
- **CRITICAL**: Block merge immediately (active secrets, exploitable RCE)
- **HIGH**: Require remediation before merge (SQLi, auth bypass, reachable CVEs)
- **MEDIUM**: Document and track (XSS, missing validation)
- **LOW**: Note for future improvement

Note: For full incident response, threat hunting, or forensic investigation, use the `forensic-specialist` agent instead.
