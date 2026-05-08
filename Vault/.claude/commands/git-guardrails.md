Enforce safe git practices for AI-assisted development — conventional commits, branch safety, and pre-action checks. Use before any git operation in a GitHub-enabled project, or when setting up a new project with git.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a git operation, repo, or concern is provided: $ARGUMENTS

## Purpose

AI working with git can cause irreversible damage: force pushes to main, commits without context, lost work, broken history. This skill enforces safe defaults — check before acting, conventional commit format, branch protection, and clear rollback paths.

---

## Rules — Non-Negotiable

**Before any git operation:**
- State what the operation will do
- State whether it is reversible
- If irreversible or destructive: STOP and confirm with operator before executing

**Destructive operations require explicit confirmation:**
- `git push --force` or `git push -f` — always confirm, never assume
- `git reset --hard` — always confirm
- `git checkout .` or `git restore .` — always confirm (discards uncommitted work)
- `git clean -f` — always confirm
- Deleting a branch — always confirm
- Merging to main/master — always confirm

**Never skip hooks:**
- Do not use `--no-verify`
- If a hook fails: investigate and fix the root cause — do not bypass

---

## Conventional Commits Format

All commits in AI-assisted projects use conventional commits:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `build`, `ci`

Examples:
- `feat(athena): add chapter 1 first draft to staging`
- `fix(workflow): correct prelive checklist reference`
- `docs(system): update daily-ai-config to v1.6`
- `chore(skills): add write-a-skill command to secondary machine`

---

## Branch Safety

- Never commit directly to main or master without explicit instruction
- Default: create a feature branch — `git checkout -b [type]/[description]`
- Naming: `feat/`, `fix/`, `docs/`, `chore/` prefix matching commit type
- Before merging: confirm the branch is up to date with main

---

## Pre-Operation Checklist

Before any commit:
- [ ] Changes are intentional and scoped to the current task
- [ ] No sensitive files included (.env, credentials, personal data)
- [ ] Commit message follows conventional commits format
- [ ] Hooks will pass (no --no-verify)

Before any push:
- [ ] Correct branch (not main unless explicitly authorised)
- [ ] Up to date with remote (git pull first if needed)
- [ ] No force push unless explicitly requested and risk stated

---

Every response ends with NEXT MOVE.
