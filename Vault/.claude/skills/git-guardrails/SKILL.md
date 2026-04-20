---
name: git-guardrails
description: Safe git practices for AI-assisted development. Enforces conventional commits, branch naming, and prevents destructive operations. Use when starting a build phase, setting up a repo, or when git safety is a concern.
---

# Git Guardrails

Establish safe git boundaries before any implementation begins.

## Before Starting Work

- [ ] Confirm base branch (never commit directly to main/master)
- [ ] Create a feature branch: `type/short-description` (e.g. `feat/user-auth`, `fix/login-redirect`)
- [ ] Confirm no uncommitted changes on base branch

## Commit Rules

Conventional commits format:
```
type(scope): short description

Types: feat | fix | chore | docs | refactor | test | style
```
- One logical change per commit
- Message describes WHY, not what
- Never commit: secrets, credentials, .env files, large binaries

## Dangerous Operations — Always Confirm First

- `git push --force` → propose `--force-with-lease` instead
- `git reset --hard` → confirm scope explicitly
- `git rebase -i` → confirm branch and target
- Deleting branches → confirm merged or intentionally abandoned

## Pre-Commit Checklist

- [ ] Tests pass (or test suite does not exist)
- [ ] No debug code left in
- [ ] No secrets or credentials staged
- [ ] Commit message follows convention

## Ralph Loop Compatibility

When running autonomous loops: commit after every completed task. Never batch multiple tasks into one commit. Each commit = one verifiable unit of work.