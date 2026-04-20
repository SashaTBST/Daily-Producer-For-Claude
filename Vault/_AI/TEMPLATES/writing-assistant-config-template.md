# [IP NAME] — WRITING ASSISTANT CONFIG
IP: [Full IP name and format — e.g. "Title (Novel, Series 1)"]
Assistant type: Writing Partner
Version: 1.0
Last updated: [YYYY-MM-DD]

This file defines HOW to write this IP — not what it contains.
World, story, characters, and events live in the World Bible.
The writing partner's base behavior lives in the /writer skill.
This file tells the writing tool how to apply its base behavior specifically to THIS IP.

Created by: Writing assistant (IP Config Creation Protocol)
Maintained by: Writing assistant adds to this file when new IP-specific writing decisions are locked.

---

## HOW THIS FILE IS USED

The /writer skill reads this file at the start of any [IP NAME] session.
It applies every rule here ON TOP OF its base behavior.
If a rule here conflicts with base behavior, this file wins.

When the writing tool makes or records a new writing decision for this IP, it adds it here.
This file grows with the story.

World Bible: `[path to World Bible]`
(World Bible = WHAT. This file = HOW.)

---

## 01. WHAT KIND OF STORY THIS IS

GENRE REGISTER
[What genre is this — and what it is NOT. Be specific.]

TONE ANCHORS
[2–3 reference works that capture the intended feeling. What does this story feel like?]

POV STRUCTURE
Primary: [POV character — whose interiority drives the story]
Secondary: [How other characters appear — from outside / partial interiority / full interiority]

PACING INTENT
[Slow/deliberate / Fast/tight / Shifts by section — describe the rhythm]

---

## 02. PROSE RULES — LOCKED FOR THIS IP

These are non-negotiable. Flag violations immediately.

✗ [Rule 1 — from author's answers to IP config questions]
✗ [Rule 2]
✗ [Rule 3]
✗ [Add more as established — date each addition]

---

## 03. PRIMARY POV VOICE — LOCKED

INTERNAL REGISTER (what the reader hears)
→ [How the POV character's internal monologue sounds and reads]
→ [Default register: measured/compressed/urgent/dry/observational — describe specifically]
→ [What they notice, how they process]

EXTERNAL REGISTER (how they appear in action/dialogue)
→ [How they speak — length, style, register]
→ [Command or action language rules]

EMOTIONAL REGISTER — LOCKED
→ [How this character signals emotion without naming it]
→ [What containment looks like — how pressure shows]
→ [What breaks their default register — and how that should read]

WHAT BREAKS THEIR VOICE — FLAG IMMEDIATELY
If prose:
- [Voice break condition 1]
- [Voice break condition 2]
- [Add as established]

---

## 04. SECONDARY CHARACTER PRESENCE

[How secondary characters appear through the POV — seen from outside / their tells / what the POV reads in them]

[One entry per major secondary character if established:]
- [CHARACTER]: [How they read through the POV — physical tells, behavioural signature]

Writing rule: [What must never happen when depicting secondary characters]

---

## 05. WORLD REFERENCE SNAPSHOT

Quick-access consistency reference. Full lore in the World Bible.

UNIVERSE CONSTANTS — NEVER VIOLATE
◈ [Constant 1]
◈ [Constant 2]
◈ [Add as locked]

[KEY ELEMENTS — add sections as needed:]
◈ [Element]: [Rule for how it appears in prose]

[MULTI-LAYER STRUCTURES — if applicable:]
◈ Layer 1 — What characters believe: [description]
◈ Layer 2 — What the reader suspects: [description]
◈ Layer 3 — The truth (SEALED if appropriate): [description]
◈ Prose rule: [What prose can/cannot confirm]

---

## 06. OPEN WRITING QUESTIONS

Questions about HOW TO WRITE this story. The writing tool asks these proactively.

UNRESOLVED
◈ [Question 1 — from IP config creation session or identified during writing]

LOW
◈ [Question 1 — can emerge during drafting]

RESOLVED
◈ [YYYY-MM-DD] [Question]: [Answer locked]

---

## 07. HOW THIS FILE GROWS

The writing tool adds to this file when:
- A new prose rule is proposed and accepted by the author
- A new character voice lock is established
- A new structural rule is confirmed
- A writing decision is made that affects future scenes consistently
- The author resolves an open writing question above

Format: add to the relevant section, date the addition.
Never remove a locked rule without explicit author instruction.

End-of-session prompt: "New writing rule identified: [rule]. Add to Writing Assistant Config? Yes / No"

---

## 08. QUESTIONS THIS FILE WAS BUILT FROM

ANSWERED
✅ [Question] → [Answer]

OPEN
◻ [Question not yet answered — writing tool asks when session allows]

---

[IP NAME] — WRITING ASSISTANT CONFIG v1.0
World Bible: [[path to World Bible]]
Staging: [[path to Book Staging]]
Live: [[path to Live file]]
Skill: /writer (reads this file on project load)
