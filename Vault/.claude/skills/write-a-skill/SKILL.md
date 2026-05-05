---
name: write-a-skill
description: Creates a new Claude Code skill in .claude/skills/[name]/SKILL.md following the Matt Pocock format standard — YAML frontmatter, canonical body structure, QA and anti-patterns sections, under 100 lines. Use when building a new vault skill, overhauling an existing skill file, or when the user types /write-a-skill.
---

Creates a new skill file using the Matt Pocock format. Outputs to `.claude/skills/[name]/SKILL.md`. Syncs to portable config unless IP-specific or personal.

If a skill name is provided: $ARGUMENTS

## Workflow

**Phase 1 — Requirements**
Ask these questions one at a time before writing anything:
1. Skill name? (slug — lowercase, hyphenated)
2. What does it do? (one sentence, third person — becomes WHAT in description)
3. When should someone use it? (triggers — becomes "Use when..." in description)
4. What input does it take? (operator prompt, file path, paste, $ARGUMENTS)
5. What does it output? (file, report, staged content, in-session response)
6. What files does it read? (configs, source of truth, templates)
7. What are the non-negotiable rules?
8. Does it need a session-start check? (improvements log, live file, etc.)

Confirm spec back before writing anything.

**Phase 2 — Write SKILL.md**

Output: `.claude/skills/[name]/SKILL.md`
If active content exceeds 100 lines: move workflow tables, examples, and reference data to `.claude/skills/[name]/REFERENCE.md`. Add pointer in SKILL.md.
See REFERENCE.md for the full format template and YAML field reference.

**Phase 3 — Register**

Add to `_AI/daily-ai-config.md` Section 04:
```
→  /[skill-name]
   [One-line description — what + when]
```

**Phase 4 — Sync**

Non-IP, non-personal: copy SKILL.md + REFERENCE.md to `Producer AI - Portable Config/Vault/.claude/skills/[name]/` in the same operation.
IP-specific or personal: local only, no sync.

## Anti-patterns
- Don't output to `.claude/commands/[name].md` — old format. Correct path: `.claude/skills/[name]/SKILL.md`.
- Don't write descriptions in first person ("I activate..."). Third person only ("Activates...").
- Don't skip YAML frontmatter — Claude can't discover the skill without it.
- Don't let SKILL.md exceed 100 lines — overflow belongs in REFERENCE.md.
- Don't sync personal or IP-specific skills to portable config.

## QA
Before calling this skill complete:
- [ ] File is at `.claude/skills/[name]/SKILL.md` (not in commands/)
- [ ] YAML frontmatter: name + description (3rd person, WHAT + WHEN, under 1024 chars)
- [ ] Body: purpose → workflow → anti-patterns → QA → reference pointer
- [ ] Active content is under 100 lines
- [ ] Overflow in REFERENCE.md if applicable
- [ ] Registered in `_AI/daily-ai-config.md` Section 04
- [ ] Synced to portable config or documented as local-only
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for full format template, YAML fields, and sync rules.

Every response ends with NEXT MOVE.
