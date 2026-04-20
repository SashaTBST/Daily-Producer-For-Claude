# Game Maker — Reference

Extended protocols for the game-maker skill. Load on demand.

---

## COMMANDS

- `/brief [project name]` — run game through Tool A question set, output GDD and PRD to staging
- `/workflow [role]` — open a specific studio role brief from Tool B
- `/build-role [role name]` — build a new role brief for Tool B
- `/status` — report which tools are built, which roles are complete, what is queued
- `/github` — surface GitHub reference scan findings for a specific tool or role
- `/build-config game [project]` — run Game Config Creation Protocol (below)
- `/add-rule [project] [rule]` — add a confirmed design rule to a project's Game Assistant Config

---

## ROLES — LOAD TRIGGERS

| Role | File | Load when |
|---|---|---|
| dev-roadmap | `roles/dev-roadmap.md` | Any build question: phasing a tool, scoping an MVP, assessing phase-gate readiness, defining what needs to be built before standalone. |
| commercialisation | `roles/commercialisation.md` | Any launch/market question: distribution channel, audience targeting, pricing model, marketing plan. Only activates when dev-roadmap confirms Phase 4 (standalone-ready). |

**Protocol:**
1. Signal: `ROLE ACTIVE: [name] — [one-line role]. Restrictions apply.`
2. Read the role file
3. Execute per that role's scope and output format
4. Return: `ROLE COMPLETE: [name] — returning to /game-maker`

Cannot activate commercialisation for a tool still in Phase 1–3. If asked: flag the gate and return to director.

---

## GAME ASSISTANT CONFIG CREATION PROTOCOL

Run when a project has no `[Project] - Game Assistant Config.md`. Ask one question at a time. Wait for each answer.

**INTRO:** "I'm going to ask you [N] questions to build the Game Assistant Config for [Project]. This tells me how to apply my tools and behavior specifically to this game."

Q1. What genre is this game? What is it NOT — any specific anti-examples? Name 1-2 reference games for feel/tone.

Q2. What does the player feel during play? Not mechanics — the emotional or psychological goal.

Q3. What are the non-negotiable design principles? These are the filters everything gets checked against.

Q4. What must this game never become? (Anti-principles — things that would break what the game is.)

Q5. Target platform(s)? PC / Console / Mobile / Multi — and what does that mean for scope?

Q6. Team size and type? Solo / small team / studio — and what AI can do vs what humans will do.

Q7. Timeline? Jam, prototype, full release, or open-ended?

Q8. What is the MVP — the minimum that must ship? List the must-have features.

Q9. What is explicitly out of scope? Features or systems to never suggest.

Q10. Engine target? UE5, Unity, Godot, custom, or undecided?

Q11. Any performance or technical constraints (target specs, frame rate, asset limits)?

Q12. What can AI fully handle for this project? What does AI assist with? What stays human?

Q13. Visual direction — reference points, colour palette, style?

Q14. Any open design questions that need answers before work can proceed?

**On completion:** Create `[ProjectFolder]/[Project] - Game Assistant Config.md` using the template at `_AI/TEMPLATES/game-assistant-config-template.md`. Report the file path.

---

## SELF-IMPROVEMENT

Operates at Levels 1 and 2. Level 3 is gated.
Framework: `_AI/SYSTEM/self-improvement-framework.md`

**Level 1 — Micro (always active)**
Adjust question depth, output structure, and role brief format in real-time.
Focus: Are briefing questions producing useful output? Is the Option A/B structure clear? Are UE5 variants adding value?

**Level 2 — Session flags (end of session)**
Triggered by: a briefing question that doesn't land, a role brief that requires rework, a routing gap.
Output: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md` (note: game-maker skill)
Do not auto-apply. Operator reviews via /improve.

**Level 3 — Macro (gated)**
Changes to tool config, question sets, or role brief format go to staging. Operator approves before SKILL.md or tool files update.
This skill does not self-write. All Level 3 changes are staged first.

Standalone: all levels active without the daily config loaded.

---

## CONTEXT WINDOW

Full protocol: `_AI/SYSTEM/context-window-protocol.md`
Game briefing sessions load large tool files — context fills faster than other skills. Act early.

1. Alert: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Include: which tool was in use, which project was being briefed, answers captured, open questions, next role to build.
3. Flush improvement flags to `_AI/MEMORY/improvements-log.md`
4. Report save. Output: "TO RESUME: Start a new chat and run /game-maker [project or tool name]"

Command: `/save-session`
