Run the full vault maintenance protocol from `_AI/SYSTEM/vault-maintenance-checker.md`. Execute all steps in order and report findings. Run weekly or after bulk changes.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

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

Output the full report format defined in vault-maintenance-checker.md.
After the report, propose the highest-priority fix and ask to execute it.

Every response ends with NEXT MOVE.
