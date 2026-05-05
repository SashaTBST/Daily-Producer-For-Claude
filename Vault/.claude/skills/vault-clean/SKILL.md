---
name: vault-clean
description: Audits skill hygiene and applies fixes to the vault. Runs format compliance checks on all skills in .claude/skills/, applies low-risk fixes with notification, proposes medium/high-risk fixes for operator approval. Complements /vault-check (structural audit — reports only). Use daily at end of session, after /vault-check to action its findings, or to formally deprecate a skill.
argument-hint: "[skills | fixes | deprecate [name] | all]"
---

Complements /vault-check. vault-check audits structure and reports. vault-clean audits skill hygiene and applies fixes.

If a mode is provided: $ARGUMENTS

## Modes

| Mode | What it does |
|------|-------------|
| `skills` | Skill hygiene audit — checks all .claude/skills/ for format compliance |
| `fixes` | Fix engine — paste a vault-check report, vault-clean classifies and proposes/applies fixes |
| `deprecate [name]` | Formally deprecates a named skill or command file |
| `all` | Skills hygiene + fix engine (provide vault-check report or skip fixes) |

If no report provided for `fixes` or `all`: ask "Paste your `/vault-check` output or run it first."

## Risk Tiers

| Tier | Examples | Action |
|------|----------|--------|
| Fix + notify | Missing NEXT MOVE footer, description >1024 chars | Apply fix, report what changed |
| Propose | Rename, archive candidate, sync drift, link fix | State the fix → wait for approval |
| Confirm | Delete file, modify live file, override portable | State risk → explicit confirmation required |

## Skill Hygiene Checks

Run for each skill in `.claude/skills/[name]/SKILL.md`:

- YAML frontmatter present (name + description fields required)
- Description ≤1024 chars
- Anti-patterns section present
- QA block present
- Ends with "Every response ends with NEXT MOVE"
- Under 100 lines — if over: REFERENCE.md present or flag
- Non-IP skills: portable config synced

Full checklist + manual review prompts: see REFERENCE.md.

## Fix Engine

When given a vault-check report:
1. Classify each finding by risk tier
2. Fix + notify items: apply in one pass, list what was changed
3. Propose items: numbered list, wait for approval per item
4. Confirm items: state risk, wait for explicit confirmation per item

Never apply a fix without reporting it. Every fix logged to `_AI/MEMORY/improvements-log.md` tagged `[SYSTEM]`.

## Deprecation Protocol

1. Add to top of file (below frontmatter if present):
   `> DEPRECATED — use /[replacement] instead. Retained for backward compatibility.`
2. Log to improvements-log.md: `[SYSTEM] Deprecated /[name] — replaced by /[replacement]`
3. Do NOT delete — retain unless operator confirms removal

## Anti-patterns

- Fixing without reporting — every change must appear in the response
- Applying propose or confirm items without approval
- Running vault-clean instead of vault-check — these are complementary, not alternatives
- Treating a missing REFERENCE.md as fix-and-notify — it requires a content decision (propose)

## QA
Before closing this skill session:
- [ ] All checks run for specified mode
- [ ] Fix + notify items applied and listed
- [ ] Propose / confirm items not applied without approval
- [ ] Every fix logged in improvements-log.md [SYSTEM]
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
