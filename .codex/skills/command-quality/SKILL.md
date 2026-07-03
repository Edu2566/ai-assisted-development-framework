---
name: command-quality
description: |
  Implements the `/quality` slash-style command for Codex. Use when the user types `/quality`, asks to run the `quality` command, or wants the AI Development Framework workflow: Run comprehensive quality checks.
---

# /quality Command

Execute this skill as the Codex-native slash-style command implementation.

## Codex Execution Rules

- Read this entire skill before acting.
- Treat `/quality` as a command invocation, not as ordinary prose.
- If the user provides text after `/quality`, treat it as command input and apply it wherever the recipe asks for user intent.
- Follow the command recipe below exactly unless a higher-priority instruction conflicts.
- When the recipe contains Claude command interpolation syntax like ``!`command` ``, run the shell command inside the backticks from the current project root and use its output as live input for that section.
- If a `~/.codex/hooks/speckit-helper.sh ...` helper command fails or returns a missing-artifact marker, report the missing prerequisite and follow the recipe's recovery guidance.
- Use any skills named by the recipe, such as `context-analysis`, `security-review`, `systematic-debugging`, or `verification-before-completion`.
- Respect read-only, planning, and phase-boundary instructions in the recipe.
- Do not edit files when the recipe says the command is read-only.

## Command Recipe

Use the quality-guardian methodology, and spawn a bounded Codex subagent only when the checks can run independently without blocking the main session.

## Quality Checks

### 0. Secrets Detection (Mandatory First)
- **All projects**: `gitleaks detect` (if available) — block on any findings

### 1. Linting
Detect and run available linters:
- **JavaScript/TypeScript**: `npm run lint`, `npx biome check .` (if biome.json present), or `npx eslint .`
- **Python**: `ruff check .`
- **Rust**: `cargo clippy`
- **Go**: `golangci-lint run` (preferred, 50+ linters) or `go vet ./...`
- **Java**: Checkstyle, PMD (`mvn pmd:check`), SpotBugs (`mvn spotbugs:check`)

### 2. Type Checking
Run type validation:
- **TypeScript**: `npm run typecheck` or `npx tsc --noEmit`
- **Python**: `mypy .` (deep) or `pyright .` (faster)
- **Rust**: `cargo check`
- **Go**: Built into compilation
- **Java**: Error Prone (compile-time)

### 3. Formatting
Check code formatting:
- **JavaScript/TypeScript**: `npx biome format --check .` or `npx prettier --check .`
- **Python**: `ruff format --check .`
- **Rust**: `cargo fmt --check`
- **Go**: `gofmt -l .`
- **Java**: `mvn spotless:check` or `./gradlew spotlessCheck`

### 4. Security & Supply Chain
- **SAST**: `semgrep scan --config auto` (cross-language, if available)
- **Go**: `govulncheck ./...` (reachability-based SCA)
- **JS/TS**: `npm audit`
- **Rust**: `cargo audit`
- **Python**: `pip-audit` or `ruff check --select S .`

### 5. Tests
Run test suite:
- **JavaScript/TypeScript**: `npm test`
- **Python**: `pytest`
- **Rust**: `cargo test`
- **Go**: `go test -race ./...`
- **Java**: `mvn test` or `./gradlew test`

### 6. Complexity Metrics
Check code complexity:
- Functions > 50 lines
- Files > 500 lines
- Cyclomatic complexity > 10

## Report Format

```
QUALITY REPORT
==============
Secrets:     [PASS/FAIL] - [findings]
Linting:     [PASS/FAIL] - [issues found]
Types:       [PASS/FAIL] - [errors]
Formatting:  [PASS/FAIL] - [files to format]
Security:    [PASS/FAIL] - [vulnerabilities]
Tests:       [PASS/FAIL] - [passed/failed/skipped]
Complexity:  [PASS/WARN] - [violations]

Overall:     [PASS/FAIL]
```
