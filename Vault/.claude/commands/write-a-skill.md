Create a new skill using the Matt Pocock format standard.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## PURPOSE

A skill is a reusable Claude Code command stored in `.claude/commands/[name].md`. This skill guides the creation of a new skill: gathering requirements, drafting the command file, and registering it in the system. Every skill created here follows the same format — consistent, under 100 lines of active prompt, with overflow in a REFERENCE section if needed.

If a skill name is provided: $ARGUMENTS

---

## PHASE 1 — REQUIREMENTS

Ask these questions one at a time before writing anything:

1. What is the skill name? (slug — lowercase, hyphenated, e.g. `write-a-skill`)
2. What does it do? (one sentence — this becomes the activation line)
3. When should someone use it? (triggers — completes the description)
4. What does it take as input? (operator prompt, file path, paste, arguments)
5. What does it output? (file, report, staged content, in-session response)
6. What files does it read? (configs, source of truth, templates)
7. What are the non-negotiable rules for this skill?
8. Does it need a session-start check? (improvements log, live file load, etc.)

When all answers are in: confirm the spec back before writing.

---

## PHASE 2 — DRAFT

Write the skill file to `.claude/commands/[name].md`.
See REFERENCE section for format standard and rules.

After writing:
- Count active prompt lines. Flag if over 100.
- If overflow: identify what can move to a `## REFERENCE` section.

---

## PHASE 3 — REGISTER

Add the skill to `_AI/daily-ai-config.md` Section 04 under the appropriate category:

```
→  /[skill-name]
   [One-line description of what it does and when to use it.]
```

Confirm registration before closing.

---

## PHASE 4 — REVIEW

Run this checklist before calling the skill complete:

- [ ] Activation line is under 1024 chars and has two sentences (what + when)
- [ ] Active prompt is under 100 lines
- [ ] Output is clearly defined
- [ ] Input is clearly defined
- [ ] Rules are explicit — no ambiguity about what the skill will/won't do
- [ ] Registered in daily-ai-config.md Section 04
- [ ] Ends with: `Every response ends with NEXT MOVE.`

---

Every response ends with NEXT MOVE.

---

## REFERENCE

### FORMAT STANDARD

Every skill file follows this structure:

```
[Activation line — one sentence. What the skill does.]

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

## PURPOSE
[What this skill does. When to use it. One paragraph.]

---

## [SECTION]
[Steps, rules, protocol — as needed]

---

Every response ends with NEXT MOVE.
```

**Rules:**
- Active prompt content: under 100 lines
- Description (activation line): max 1024 chars. Sentence 1 = what it does. Sentence 2 = when to use it.
- If content overflows 100 lines: extract reference tables, examples, and lookup data into a `## REFERENCE` section at the bottom — or document the overflow separately
- No hardcoded file paths unless the path is fixed by system design
- Every skill ends with: `Every response ends with NEXT MOVE.`
