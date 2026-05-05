---
name: write-a-skill-reference
description: Extended reference for /write-a-skill — format template, YAML fields, line count rules, sync rules, and skill categories.
type: reference
---

## FORMAT TEMPLATE

```yaml
---
name: [slug]
description: [3rd person. "Verb + object + context. Use when [triggers]." Max 1024 chars.]
argument-hint: "[optional: what $ARGUMENTS accepts, shown in UI]"
disable-model-invocation: true  # navigation/lightweight skills only — direct prompt injection, no submodel
---
```

Active content follows frontmatter. No "I will" language. Third person throughout.

Canonical body structure:

```
[Activation line — brief purpose. Third person.]
[Optional: If an X is provided: $ARGUMENTS]

## Quick start (optional — for skills with an instant run mode)

## Workflow
[Numbered phases or steps. Checklists where operator must verify.]

## Anti-patterns
- [Named failure mode — specific, not vague]
- [2–5 items]

## QA
Before closing this skill session:
- [ ] [Skill-specific check 1]
- [ ] [Skill-specific check 2]
- [ ] Output is in the correct pipeline stage (Staging / Prelive / Live)
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for [topic].

Every response ends with NEXT MOVE.
```

---

## YAML FIELDS

| Field | Required | Notes |
|-------|----------|-------|
| `name` | Yes | Slug. Lowercase, hyphenated. Matches file path. |
| `description` | Yes | 3rd person. WHAT + "Use when [triggers]". Max 1024 chars. |
| `argument-hint` | No | Short hint shown in Claude Code UI on skill selection. |
| `disable-model-invocation` | No | `true` = direct prompt injection, no submodel spawn. Use for navigation/orientation/lightweight skills (zoom-out, domain-model). |

---

## LINE COUNT

| Threshold | Meaning |
|-----------|---------|
| 100 lines | Matt Pocock quality target — everything fits in one read. Target for every SKILL.md. |
| 500 lines | Anthropic hard cap — skills above this underperform. Never exceed. |

Count includes frontmatter lines and blank lines. Count everything.

When SKILL.md exceeds 100 lines: extract tables, format examples, and lookup data to REFERENCE.md. Add a pointer line in SKILL.md: `See REFERENCE.md for [topic].`

---

## SYNC RULES

| Skill type | Sync |
|------------|------|
| Non-IP, non-personal | Copy to `Producer AI - Portable Config/Vault/.claude/skills/[name]/` |
| IP-specific | Local only — never sync |
| Personal (bookkeeping, lifestyle-plan, tax-return) | Local only — portable has a generic framework version. Never overwrite portable generic with local personal content. |

Always sync SKILL.md + REFERENCE.md together. Never partial sync.

---

## SKILL CATEGORIES (daily-ai-config Section 04)

Register the new skill under the correct header:
- DAILY OPERATIONS
- CONTENT & CREATIVE
- WRITING
- PROJECT-SPECIFIC WORKFLOWS
- PROJECT & BUSINESS
- PERSONAL
- FILE OPERATIONS
- SYSTEM
- DEV PIPELINE — MANDATORY GATE SEQUENCE
- SKILLS — PLANNING & DESIGN
- SKILLS — DEVELOPMENT
- SKILLS — WRITING & KNOWLEDGE
- SKILLS — VAULT-BUILT
