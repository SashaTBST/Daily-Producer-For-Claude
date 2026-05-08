# Commands & Skills Standard

## Canonical Location

This vault uses the multi-file skill format:

```
.claude/skills/[name]/
├── SKILL.md        # Core behavior. Under 100 lines. YAML frontmatter required.
└── REFERENCE.md    # Extended protocols, overflow content (only if SKILL.md > 100 lines)
```

Claude Code supports both `.claude/skills/[name]/SKILL.md` (multi-file) and `.claude/commands/[name].md` (single-file). Both work. When names clash, the skill takes precedence over the command. This vault uses the multi-file format exclusively — it allows REFERENCE.md overflow and is the Matt Pocock standard.

## SKILL.md Format Standard

```yaml
---
name: [name]
description: [max 1024 chars. First sentence: what it does. Second: "Use when [triggers]."]
argument-hint: "[optional: description of accepted args]"
---
```

Active instructions follow frontmatter. No bloat. No padding.

## Adding or Updating a Skill

1. Create `.claude/skills/[name]/SKILL.md` (required)
2. Create `.claude/skills/[name]/REFERENCE.md` (only if SKILL.md would exceed 100 lines)
3. **Write the QA EVIDENCE block in the plan file before marking the skill complete:**
   - Portable sync is confirmed IN the QA EVIDENCE block — not as a separate step
   - Non-IP skill → sync to `Producer AI - Portable Config/Vault/.claude/skills/[name]/` then confirm in QA block
   - Personal skill → portable has generic framework version. Never overwrite portable generic with local personalised content. Confirm N/A in QA block.
   - IP-specific skill → local-only. Confirm N/A (IP-specific) in QA block.
4. **Sync is not complete until it appears in the QA EVIDENCE block.** If it isn't written there, it wasn't done.
5. Any edit to a non-IP skill file = edit both files AND update QA EVIDENCE before the operation is complete.

## Sub-Skill (Role) Standard

Director skills can spawn specialised role sub-skills. These are reference files explicitly loaded by the director — not auto-discovered.

```
.claude/skills/[director]/
├── SKILL.md           # Director behavior. Includes ROLES section with load table.
├── REFERENCE.md       # ROLES — LOAD TRIGGERS table with explicit load conditions.
└── roles/
    └── [role].md      # Sub-skill. YAML frontmatter required.
```

**Role file format:**
```yaml
---
name: [role-name]
parent: [director-name]
role: [one-line summary of what this role owns]
restrictions:
  - [what it does NOT do]
  - [what it defers to the director]
---
```

**Loading protocol:**
1. Director signals: `ROLE ACTIVE: [name] — [role]. Restrictions apply.`
2. Director reads the role file
3. Claude executes per the role's scope and output format
4. Role returns: `ROLE COMPLETE: [name] — returning to /[director]`

**Limits:**
- Each role file: under 75 lines
- Director SKILL.md: stays under 100 lines — move commands to REFERENCE.md when needed
- Roles are loaded explicitly — never auto-discovered or auto-loaded

**Sync:** Non-IP role files sync to portable in the same operation as the director skill. Confirmed in QA EVIDENCE block.
IP-specific roles stay local-only. Confirmed N/A in QA EVIDENCE block.

## Sync Rule (New Machines)

When setting up on a new machine, copy from portable config:
- `.claude/skills/` → local `.claude/skills/`
- `.claude/rules/` → local `.claude/rules/`
- `.claude/settings.json` → local `.claude/settings.json`

Do not copy IP-specific files (any config, daily note, or skill marked local-only).

**Portable sync scope — system files only.**
Portable config receives: non-IP skills, rules, settings. Optimised for clean system setup on a new machine without any project IP.
Project output, assistant configs, and working files are NOT synced to portable — they belong to their project's GitHub repo. For project sync: see `_AI/SYSTEM/github-standards.md`.
