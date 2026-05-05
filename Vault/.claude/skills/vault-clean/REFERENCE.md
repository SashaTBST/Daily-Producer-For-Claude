---
name: vault-clean-reference
parent: vault-clean
role: Full skill hygiene checklist, fix protocols, and improvements-log format
---

## Full Skill Hygiene Checklist

| Check | Pass condition | Fail tier | Action |
|-------|---------------|-----------|--------|
| YAML frontmatter present | `---` block at top with name + description | Fix + notify | Add missing fields |
| Description ≤1024 chars | Count chars | Fix + notify | Truncate to 1024 |
| `argument-hint` field | Present if skill takes args | Fix + notify | Add from skill behaviour |
| Active instructions follow frontmatter | Content after `---` block | Propose | Flag for operator review |
| `## Anti-patterns` section | Present | Propose | Flag — operator adds content |
| `## QA` block | Present | Propose | Flag — operator adds content |
| Ends with "Every response ends with NEXT MOVE" | Last non-empty line | Fix + notify | Auto-append |
| Under 100 lines | Line count ≤100 | Propose | Flag; check if REFERENCE.md needed |
| REFERENCE.md present if SKILL.md >100 lines | File exists | Propose | Suggest create |
| Portable sync (non-IP) | Content matches portable | Propose | Sync local → portable |

### Manual Review Prompts (not auto-checked — use when reviewing a specific skill)

- **Phantom commands:** Does the skill reference any `/command` not in the vault's skill registry?
- **Scope alignment:** Does the skill do anything not mentioned in its description?
- **Dead instructions:** Are there rules that no longer match how the skill is actually used?

---

## Fix Protocols

### Rename (Propose)
```
RENAME: [current] → [proposed] — rule: [naming convention violated]
```
On approval: rename file, grep vault for all `[[wikilinks]]` referencing old name, update each, report all changes.

### Archive (Propose)
```
ARCHIVE: [file] — reason: [stale / superseded / completed]
```
On approval: create `[ProjectFolder]/Archive/` if missing, move file, append ` - Archive` to filename before `.md`, log to improvements-log.md.

### Sync to portable (Propose)
```
SYNC: [file] — local is more current → copy to portable
```
On approval: copy file to `Producer AI - Portable Config/Vault/` equivalent path, confirm match.

### Deprecate (Propose)
Add below frontmatter (or at top of file if no frontmatter):
```
> DEPRECATED — use /[replacement] instead. Retained for backward compatibility.
```
Then log — see entry format below.

### Delete (Confirm)
```
DELETE: [file] — reason: [why]
RISK: Irreversible. Archive recommended as safer alternative.
```
Only proceed on explicit confirmation: "yes delete" / "confirmed" / "delete it".

---

## Improvements Log Entry Format

Every vault-clean fix logs one entry per action in `_AI/MEMORY/improvements-log.md`:

```
---
TRIGGER: [SYSTEM] [YYYY-MM-DD] — vault-clean: [action description]
OBSERVATION: [what was wrong]
CHANGE: [what was done]
STATUS: Applied
```

Example:
```
---
TRIGGER: [SYSTEM] 2026-04-26 — vault-clean: added NEXT MOVE footer to /triage-issue
OBSERVATION: SKILL.md missing "Every response ends with NEXT MOVE" footer
CHANGE: Appended footer to .claude/skills/triage-issue/SKILL.md
STATUS: Applied
```

---

## Recommended Cadence

| Frequency | Mode | Trigger |
|-----------|------|---------|
| Daily (end of session) | `skills` | After any skill was edited this session |
| After /vault-check | `fixes` | Paste vault-check report to action findings |
| Weekly | `all` | Regular maintenance — provide fresh vault-check report |
| On demand | `deprecate [name]` | When retiring a skill or command |
