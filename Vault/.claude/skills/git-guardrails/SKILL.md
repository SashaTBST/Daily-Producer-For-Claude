---
name: git-guardrails
description: Enforces safe git practices for AI-assisted development — conventional commits, branch safety, commit granularity, amend rules, and pre-action checks. Use before any git operation in a GitHub-enabled project, or when the user says "commit", "push", "merge", "reset", or "squash".
argument-hint: "[git operation, repo, or concern]"
---

AI working with git can cause irreversible damage: force pushes to main, commits without context, lost work, broken history. This skill enforces safe defaults.

If a git operation, repo, or concern is provided: $ARGUMENTS

## Anti-patterns

- Executing a destructive operation without stating what it does and confirming — force push, reset --hard, checkout ., clean -f always require explicit confirmation.
- Using `--no-verify` to bypass hooks — investigate and fix the root cause, never bypass.
- Committing to main directly — always work on a feature branch unless explicitly authorised.
- Vague commit messages — "updates" or "fix stuff" are not commits. Conventional format always.
- Assuming approval because the operator is moving fast — slow down and confirm destructive ops.
- Amending a commit already pushed to remote — after push, new commit always.
- Batching unrelated AI-generated changes into one commit — break by logical unit.

## Rules — Non-Negotiable

**Before any git operation:**
- State what the operation will do
- State whether it is reversible
- If irreversible or destructive: STOP and confirm before executing

**Destructive operations — always confirm:**
`git push --force`, `git reset --hard`, `git checkout .`, `git restore .`, `git clean -f`, deleting a branch, merging to main/master.

**Never skip hooks** — if a hook fails: investigate and fix the root cause.

## Conventional Commits Format

```
<type>(<scope>): <description>
[optional body]
[optional footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `build`, `ci`

## Commit Granularity

One logical unit per commit. AI output often spans multiple concerns — break it before staging.
Each commit must be independently reversible. If unsure how to split: ask "what is the smallest rollback target here?"
Full AI-output splitting guide: see REFERENCE.md.

## Amend Rule

Safe before push only. After push → new commit always. Never amend shared history.
Exception: fixing an unpushed commit message only (`git commit --amend --only`) is always safe.

## Branch Safety

- Never commit directly to main/master without explicit instruction
- Default: create a feature branch — `git checkout -b [type]/[description]`
- Naming: `feat/`, `fix/`, `docs/`, `chore/` prefix matching commit type
- Before merging: confirm branch is up to date with main
- After merge: delete the branch. Enable auto-delete in GitHub repo settings.

## QA
Before any commit or push:
- [ ] Changes intentional and scoped to current task
- [ ] No sensitive files (.env, credentials, personal data)
- [ ] Commit message follows conventional commits format
- [ ] No `--no-verify` — hooks will pass
- [ ] Correct branch (not main unless explicitly authorised)
- [ ] No force push unless explicitly requested and risk stated
- [ ] AI-generated changes broken into logical units before staging

Every response ends with NEXT MOVE.
