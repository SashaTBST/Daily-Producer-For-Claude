Establish and maintain a shared vocabulary across all skills, configs, and documents in this vault. Use when introducing new terms, when terminology is inconsistent across files, or when onboarding a new project or skill.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a term, domain, or file to audit is provided: $ARGUMENTS

## Purpose

Ubiquitous language means every term has one definition, used consistently everywhere — in skill files, configs, daily notes, project files, and AI responses. When terms drift (staging vs draft, live vs published, entity vs project), the system degrades. This skill establishes and enforces shared vocabulary.

---

## Process

**Step 1 — Define scope**
Ask: what is being standardised? Options:
- A new term being introduced
- A domain audit (check consistency across a project or skill)
- A full vault vocabulary check

**Step 2 — Identify the term(s)**
For each term: state the canonical definition. One sentence. Unambiguous.

Check for: synonyms in use (e.g. "staging file" vs "working draft"), near-synonyms that should be different (e.g. "prelive" vs "preview"), terms used inconsistently across files.

**Step 3 — Propose canonical definitions**
For each term: propose the single correct definition and the correct usage.

Format:
```
Term: [term]
Definition: [one sentence — what it means in this system]
Use when: [context]
Do not confuse with: [near-synonym and how they differ]
```

**Step 4 — Audit existing usage**
Check the files in scope for inconsistent usage. Flag any file using a non-canonical term.

**Step 5 — Update (if instructed)**
If the operator approves: update files to use canonical terms. Stage changes — do not write directly to live files.

---

## Creative Exception — Non-Negotiable

This skill applies to: skill files, configs, daily notes, operator communication, project management files.

This skill does NOT apply to: generated prose, dialogue, scripts, narrative content, or any creative output where voice and style require natural language variation.

When CREATIVE MODE is active (writer, game-maker skills): ubiquitous language rules apply to operator communication only — not to the generated content itself.

---

## Vault Vocabulary — Core Terms

| Term | Definition |
|------|-----------|
| Staging | Working version of a file. All drafts happen here. |
| Prelive | Push request. Ready for operator review before promotion. |
| Live | Authoritative published version. Strict gate. |
| Source of Truth | The live file or document that represents the current canonical state of a project. |
| Vertical slice | Smallest unit of work delivering real value end-to-end. |
| HITL | Human-in-the-loop. Requires operator approval before or during execution. |
| AFK | Autonomous. Agent completes without operator input. |
| Entity | A business, brand, or brand project in the registry. |
| Brand Project | A project under a Brand, under a Business. |
| Skill | A reusable Claude Code command in `.claude/commands/`. |
| Config | An assistant configuration file — project-specific or global. |

---

Every response ends with NEXT MOVE.
