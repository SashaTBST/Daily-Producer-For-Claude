---
name: vault-check
description: Runs the full vault maintenance protocol from vault-maintenance-checker.md — naming violations, stale files, broken references, orphaned files, config coverage. Use weekly or after bulk changes to the vault.
argument-hint: "[step: naming | stale | refs | orphans | configs | sources | repos]"
---

Load `_AI/SYSTEM/vault-maintenance-checker.md` and execute the full maintenance protocol — Steps 1 through 10 in order.

If a specific step is passed as an argument, run that step only:
- `naming` → Step 1
- `stale` → Step 2
- `refs` → Step 3
- `orphans` → Step 5
- `configs` → Step 7
- `sources` → Step 8
- `repos` → Step 10

## Anti-patterns

- Running vault-check and reporting "all clear" without actually checking — this skill executes the protocol, not a summary.
- Flagging issues without proposing a fix — always propose the highest-priority fix after the report.
- Treating stale files as deletable without confirmation — flag only. Never delete without operator instruction.
- Skipping steps because they seem low-priority — run in order. Surface everything.

## Output

Report format defined in `_AI/SYSTEM/vault-maintenance-checker.md`.

After the report: propose the highest-priority fix and ask to execute it.

## QA
Before closing this skill session:
- [ ] All 10 steps executed (or the specified step only)
- [ ] Report uses format from vault-maintenance-checker.md
- [ ] Issues flagged, not auto-fixed
- [ ] Highest-priority fix proposed after report
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
