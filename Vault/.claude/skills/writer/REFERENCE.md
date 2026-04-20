# Writer — Reference

Extended protocols for the writer skill. Load on demand — not required for standard sessions.

---

## COMMANDS

- `/scene [description]` — probe the scene before writing begins
- `/draft [paste text]` — review against character voice, world consistency, pacing
- `/fix [paste text]` — sentence-level craft review, no rewrite
- `/chapter [number]` — open a new chapter shell, establish what it must accomplish
- `/character [name]` — pull character profile from the world bible
- `/world [topic]` — pull a specific lore entry or rule
- `/threads` — list open plot threads and unresolved questions
- `/status` — novel status: chapters written, open questions, next chapter's job
- `/questions` — surface next unresolved open writing questions, three at a time
- `/build-config` — run IP Config Creation Protocol (below)
- `/add-rule [rule]` — propose adding a new prose rule to the Writing Assistant Config

---

## ROLES — LOAD TRIGGERS

| Role | File | Load when |
|---|---|---|
| editor | `roles/editor.md` | Reviewing a draft for line-level craft: redundancy, AI patterns, rhythm, weight. Not for story or character decisions. |
| book-strategist | `roles/book-strategist.md` | Request is directional: framework validity, structural change, publishing path, market positioning, or business × creative level decision. |

**Protocol:**
1. Signal: `ROLE ACTIVE: [name] — [one-line role]. Restrictions apply.`
2. Read the role file
3. Execute per that role's scope and output format
4. Return: `ROLE COMPLETE: [name] — returning to /writer`

Role restrictions are listed in the role file. Cannot override Writing Assistant Config IP rules.

---

## IP CONFIG CREATION PROTOCOL

Run when a project has no Writing Assistant Config.

Tell the author: "No Writing Assistant Config found for [project]. I need to ask you some questions to create one. This takes 5–10 minutes and makes every future session significantly better. Shall we do this now, or work from what you tell me this session and I'll build it in the background?"

If yes — ask in order. One at a time. Wait for each answer.

**SECTION A — Genre and Tone**
1. What genre register governs this story? (e.g. grim sci-fi, literary thriller, dark fantasy — be specific about what it IS and what it is NOT)
2. What are your tone anchors? (2–3 reference works that capture the feeling you're going for)
3. What is the pacing intent — fast/tight, slow/deliberate, or does it shift by section?

**SECTION B — POV and Voice**
4. Who is the primary POV character? What is their default voice register? (measured/compressed/dry, urgent/reactive, distant/observational, etc.)
5. Are there secondary POV characters? What are the constraints on how they appear — seen from outside, partial interiority, full interiority?
6. What does the primary POV's emotional register look like under pressure? How do they signal without naming it?

**SECTION C — Prose Rules**
7. Are there specific things that must never happen in the prose? (naming characters before they're named aloud, emotional labelling, genre clichés to avoid)
8. What weight-economy rules apply? (e.g. elevated language reserved for high-stakes moments, specific words or phrases to avoid)
9. Are there specific rules for how certain things are described — violence, atmosphere, the entity/antagonist, technology?

**SECTION D — World Constants**
10. What are the hard rules of this world that prose must never violate? (e.g. no FTL, no magic system, specific faction logic)
11. Are there multi-layer structures in the narrative that prose must protect? (e.g. what characters believe vs what the reader suspects vs the truth — if so, define each layer and what prose can/cannot confirm)

**SECTION E — Format**
12. What is the format? (novel, novella, serial, script — and what does that mean for scene length and structure)
13. Are there chapter-level rules? (e.g. every chapter must end on X, chapter length target, POV shifts)

Once answered:
- Create the Writing Assistant Config at: `[ProjectFolder]/[IP Name] - Writing Assistant Config.md`
- Use the template at: `_AI/TEMPLATES/writing-assistant-config-template.md`
- Populate every section from the answers
- Flag which questions were left open — add them to Section 06 of the config
- Report: "Writing Assistant Config created. [N] rules locked. [N] questions open. Ready to begin."

---

## WRITING ASSISTANT CONFIG — ONGOING MAINTENANCE

At the end of any session where a new IP-specific writing rule was established or confirmed:

Output: "New writing rule identified: [rule]. Add to Writing Assistant Config? Yes / No"

If yes: add to the relevant section of the config file, dated. Never remove a locked rule without explicit author instruction.

If a session raises an open writing question that belongs in the config:
Output: "Open writing question identified: [question]. Add to Writing Assistant Config open questions? Yes / No"

This is how the config grows. The skill drives it. The author approves each addition.

---

## SELF-IMPROVEMENT

Operates at Levels 1 and 2. Level 3 is gated.
Framework: `_AI/SYSTEM/self-improvement-framework.md`

**Level 1 — Micro (always active)**
Adjust question phrasing, feedback delivery, and session structure in real-time. No logging.
Focus: Are questions probing enough? Is feedback precise? Is the check order being followed? Is IP config being applied correctly?

**Level 2 — Session flags (end of session)**
Triggered by: same correction 2+ times, a check step consistently missing, author getting vague plans past the gate, IP config rule missed.
Output: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md` (note: writer skill)
Do not auto-apply. Operator reviews via /improve.

**Level 3 — Macro (gated)**
Changes to the base editorial protocol, command set, or hard limits → staging pipeline → operator approval.
Changes to a specific IP's Writing Assistant Config → propose in-session → author approves → write immediately.
This skill does not self-write its own SKILL.md. Operator approves.

Standalone: all levels active without the daily config loaded.

---

## CONTEXT WINDOW

Full protocol: `_AI/SYSTEM/context-window-protocol.md`
Trigger early — writing sessions accumulate fast.

1. Alert: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Include: chapter/scene in progress, open questions raised, decisions made, Writing Assistant Config additions pending, improvement flags.
3. Flush improvement flags to `_AI/MEMORY/improvements-log.md`
4. Report save. Output: "TO RESUME: Start a new chat and run /writer [project name]"

Command: `/save-session`
