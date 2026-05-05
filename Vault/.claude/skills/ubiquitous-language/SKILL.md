---
name: ubiquitous-language
description: Establishes and maintains a shared vocabulary across all skills, configs, and documents in the vault — one definition per term, used consistently everywhere. Use when introducing new terms, when terminology is inconsistent across files, or when onboarding a new project or skill.
argument-hint: "[term, domain, or file to audit]"
---

One term. One definition. Used consistently everywhere — in skill files, configs, daily notes, project files, and AI responses. When terms drift, the system degrades.

If a term, domain, or file to audit is provided: $ARGUMENTS

## Anti-patterns

- Defining terms vaguely — "it means roughly X" is not a definition. One sentence, unambiguous.
- Proposing changes directly to live files — stage all vocabulary updates.
- Applying vocabulary rules to generated creative content — this skill governs operator communication and system files only, not prose, dialogue, or narrative output.
- Letting near-synonyms coexist without differentiation — if two terms are in use, one is wrong. Name the canonical one and kill the other.

## Process

**Step 1 — Define scope**
What is being standardised? New term / domain audit / full vault vocabulary check.

**Step 2 — Identify the term(s)**
Check for: synonyms in use, near-synonyms that should be different, terms used inconsistently across files.

**Step 3 — Propose canonical definitions**
```
Term: [term]
Definition: [one sentence — what it means in this system]
Use when: [context]
Do not confuse with: [near-synonym and how they differ]
```

**Step 4 — Audit existing usage**
Check files in scope for inconsistent usage. Flag any file using a non-canonical term.

**Step 5 — Update (if instructed)**
Operator approves → update files to use canonical terms. Stage changes — do not write to live.

## Vault Vocabulary — Core Terms

| Term | Definition |
|------|-----------|
| Staging | Working version of a file. All drafts happen here. |
| Prelive | Push request. Ready for operator review before promotion. |
| Live | Authoritative published version. Strict gate. |
| Source of Truth | The live file representing the current canonical state of a project. |
| Vertical slice | Smallest unit of work delivering real value end-to-end. |
| HITL | Human-in-the-loop. Requires operator approval before or during execution. |
| AFK | Autonomous. Agent completes without operator input. |
| Entity | A business, brand, or brand project in the registry. |
| Brand Project | A project under a Brand, under a Business. |
| Skill | A reusable Claude Code command in `.claude/skills/[name]/SKILL.md`. |
| Config | An assistant configuration file — project-specific or global. |

## QA
Before closing this skill session:
- [ ] Every proposed term has a one-sentence unambiguous definition
- [ ] Near-synonyms distinguished — not left to coexist
- [ ] All updates staged, not written to live
- [ ] Creative content excluded from vocabulary enforcement
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
