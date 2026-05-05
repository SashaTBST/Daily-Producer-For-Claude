---
name: git-guardrails-reference
parent: git-guardrails
role: Extended protocols for squash/rebase and AI-assisted commit granularity
---

## Squash / Rebase Protocol

**When to squash:**
- Multiple WIP/fixup commits on a local branch before opening a PR → squash into one per logical change
- Fixup commits (typo, forgot file) → squash into parent before push

**When NOT to squash:**
- Logically separate changes — keep as separate commits even on the same branch
- After pushing to remote — never rewrite shared history

**Squash before opening PR. Not after.**

**How to squash — operator runs manually (AI cannot execute `-i` flag):**
```
git rebase -i HEAD~[N]
```
Mark WIP commits as `squash` or `s`. Fixup commits (no message change): use `fixup` or `f`.

**AI role in squash:** stage the files, draft the consolidated commit message. Operator executes the rebase.

**Rebase rule:**
- Local-only branches: rebase onto main freely
- Pushed branches: never rebase — use merge or squash-merge PR instead

---

## AI-Assisted Commit Granularity

AI generates large outputs — one prompt can produce schema + service + route + tests. Don't commit as one block.

**Protocol:**
1. Review full AI output before staging anything
2. Identify logical units: schema change / service layer / route handler / tests = separate commits
3. Stage and commit each unit independently
4. Commit type must match the unit: `feat` for new behaviour, `refactor` for restructure, `test` for tests only

**Frequency:** commit after each working logical unit — don't wait for the feature to be "done". Small commits = precise rollback targets.

**AI attribution:** Claude Code appends `Co-Authored-By` automatically. No manual action required.
