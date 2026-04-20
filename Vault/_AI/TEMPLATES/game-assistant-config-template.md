# [PROJECT NAME] — GAME ASSISTANT CONFIG
Project: [Game project name]
Engine: [Target engine — UE5, Unity, Godot, custom, etc.]
Platform: [Target platform(s)]
Assistant type: Game Partner
Version: 1.0
Last updated: [YYYY-MM-DD]

This file defines HOW to work on this game project — not what the game is.
Game facts, design decisions, and features live in the GDD and PRD.
The game-maker skill's base behavior (briefing, studio workflow, config library) lives in the /game-maker skill.
This file tells the game tool how to apply its behavior specifically to THIS project.

Created by: Game-maker assistant (Game Config Creation Protocol)
Maintained by: Game-maker assistant adds to this file when new project-specific rules are established.

---

## HOW THIS FILE IS USED

The /game-maker skill reads this file when working on [PROJECT NAME].
It applies every rule here ON TOP OF its base tooling behavior.
If a rule here conflicts with base behavior, this file wins.

GDD: `[path to GDD-Staging]`
PRD: `[path to PRD-Staging]`
World/Narrative: `[path if applicable]`

---

## 01. WHAT KIND OF GAME THIS IS

GENRE AND TONE
[What genre. What it is NOT — specific anti-examples. Reference games for tone/feel.]

CORE PLAYER EXPERIENCE
[What does the player feel during play? Not mechanics — the emotional/psychological goal.]

DESIGN PRINCIPLES — NON-NEGOTIABLE
These are the filters everything gets checked against. If a feature or decision violates these, flag it.
→ [Principle 1]
→ [Principle 2]
→ [Principle 3]

WHAT THIS GAME IS NOT
✗ [Anti-principle 1 — what this game must never become]
✗ [Anti-principle 2]

---

## 02. SCOPE — LOCKED CONSTRAINTS

TARGET PLATFORM
[PC / Console / Mobile / Multi — and what that means for scope]

TEAM SIZE AND TYPE
[Solo / Small team / Studio — and what AI can do vs what humans will do]

TIMELINE
[Rough target — jam, prototype, full release]

BUDGET CONSTRAINTS
[Any relevant constraints that affect feature decisions]

MUST-SHIP SCOPE (MVP)
[What the minimum viable version must include. Anything not on this list is post-MVP.]
→ [Feature 1]
→ [Feature 2]

OUT OF SCOPE — DO NOT SUGGEST
✗ [Feature/system explicitly out of scope]
✗ [Technology to avoid]

---

## 03. TECHNICAL CONSTRAINTS

ENGINE-SPECIFIC RULES
[Any constraints from the target engine that affect design decisions]

PERFORMANCE TARGETS
[Target specs, frame rate, platform limits]

ASSET CONSTRAINTS
[Poly budget approach, texture limits, audio format, etc. if relevant]

WHAT AI CAN FULLY HANDLE FOR THIS PROJECT
→ [Area 1 — e.g. "narrative, dialogue, quest flow"]
→ [Area 2]

WHAT AI ASSISTS WITH FOR THIS PROJECT
→ [Area — e.g. "gameplay systems — AI designs, human implements"]

WHAT REMAINS HUMAN FOR THIS PROJECT
→ [Area — e.g. "all art assets — AI only supports concept briefs"]

---

## 04. TONE AND AESTHETIC — LOCKED

VISUAL DIRECTION
[Reference points, colour palette direction, visual style — brief]

AUDIO DIRECTION
[Music register, SFX approach, voice acting if applicable]

NARRATIVE REGISTER (if applicable)
[Tone of dialogue and writing — what it sounds like]

WHAT BREAKS AESTHETIC — FLAG IMMEDIATELY
✗ [Aesthetic violation 1]
✗ [Aesthetic violation 2]

---

## 05. OPEN DESIGN QUESTIONS

Questions the game-maker tool asks proactively when they block progress.

HIGH — BLOCKS DESIGN
◻ [Question 1]

MEDIUM — SHAPES DIRECTION
◻ [Question 1]

RESOLVED
◈ [YYYY-MM-DD] [Question]: [Answer locked]

---

## 06. HOW THIS FILE GROWS

The game-maker tool adds to this file when:
- A design principle is confirmed or refined
- A scope constraint is locked
- A technical decision is made that affects all future work on this project
- A question is resolved that other questions depend on

End-of-session prompt: "New design constraint identified: [constraint]. Add to Game Assistant Config? Yes / No"

---

## 07. QUESTIONS THIS FILE WAS BUILT FROM

ANSWERED
✅ [Question] → [Answer]

OPEN
◻ [Question not yet answered]

---

[PROJECT NAME] — GAME ASSISTANT CONFIG v1.0
GDD: [[path to GDD-Staging]]
PRD: [[path to PRD-Staging]]
Skill: /game-maker (reads this file when project is active)
