---
name: write-a-skill
description: Create new agent skills with proper structure, progressive disclosure, and bundled resources. Use when user wants to create, write, or build a new skill, or when /new-skill is invoked.
---

# Write a Skill

## Process

Run the full **Build Protocol** (`.claude/rules/build-protocol.md`) before writing any files.
Phases in order: GATHER → RESEARCH → PROTOTYPE → GRILL → STAGE → PRELIVE/LIVE.

Do not draft or write anything until GATHER questions are answered and GRILL has passed.
The inbound gate in build-protocol.md must fire first.

## Skill Structure

```
.claude/skills/skill-name/
├── SKILL.md           # Main instructions (required)
├── REFERENCE.md       # Detailed docs (if needed)
├── EXAMPLES.md        # Usage examples (if needed)
└── scripts/           # Utility scripts (if needed)
```

## SKILL.md Template

```md
---
name: skill-name
description: What it does. Use when [specific triggers].
---

# Skill Name

## Quick start
[Minimal working example]

## Workflows
[Step-by-step processes with checklists for complex tasks]

## Advanced features
[Link to separate files: See REFERENCE.md]
```

## Description Requirements

The description is the only thing Claude sees when deciding which skill to load.

- Max 1024 chars
- First sentence: what it does
- Second sentence: "Use when [specific triggers]"

**Good:** `Extract text and tables from PDF files. Use when working with PDF files or user mentions PDFs.`
**Bad:** `Helps with documents.`

## When to Split Files

Split into REFERENCE.md when:
- SKILL.md would exceed 100 lines
- Content has distinct domains
- Advanced features are rarely needed

## Review Checklist

- [ ] Description includes "Use when..." triggers
- [ ] SKILL.md under 100 lines
- [ ] No time-sensitive info
- [ ] Consistent terminology
- [ ] References one level deep only
