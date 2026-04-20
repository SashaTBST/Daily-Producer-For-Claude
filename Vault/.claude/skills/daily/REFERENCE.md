# Daily — Reference

Extended protocols for the daily skill. Load on demand.

---

## PROJECT AUDIT PROTOCOL

Run at every session start via SESSION START step 4. Also available as `/audit`.
Full audit spec: `_AI/SYSTEM/system-architecture.md` (Project Audit Protocol section)

**For each active project in the registry:**

**Step 1 — Skill match**
What skills apply to this project type?
- Novel/writing project → `/writer`
- Content/brand → `/content`
- Game project → `/game-maker`
- Business/client/production → `/ai-producer`
- New type with no matching skill → flag: "No skill for [type]. Run `/new-skill [type]` to create one."

**Step 2 — Assistant config check**
For each matching skill, does `[Project] - [SkillType] Assistant Config.md` exist in the project folder?
- Yes → note it. Flag if not updated in 30+ days and project is active.
- No → flag: "Missing [SkillType] Assistant Config for [Project]. Run `/build-config [skill] [project]`"

**Step 3 — Source of truth check**
Does the project have a current source of truth file (World Bible, Content Strategy, GDD, Business Plan)?
- No → flag: "No Source of Truth for [Project]. Should one be created?"
- Exists but wrong format (code file, placeholder, non-documentation) → flag

**Step 4 — Stale working files**
Any staging files untouched for 7+ days?
- Flag for action or cleanup.

**Step 5 — Sub-project check** (for Brands with multiple Brand Projects)
- Any empty sub-project folders with no files → flag: "Empty: [folder]. Active or inactive?"
- Each sub-project should have its own assistant config and source of truth
- Any unregistered folders inside a project directory → flag and offer to register

**Step 6 — Brand name check**
- Does the project name look like a placeholder or working title? (generic descriptor, version number, obvious temp name)
- Flag: "[Name] may be a placeholder. Suggest proper brand name when more project information is available."
- Never rename automatically — flag and propose only when operator confirms

**Audit output format:**
```
PROJECT AUDIT — [date]

[Project Name]
  Skills:            /writer ✅ | /content — no config
  Assistant Configs: Writing Assistant Config ✅ | Content Assistant Config ✗ → flag
  Source of Truth:   World Bible ✅ | Content Strategy ✗ → flag
  Working files:     Chapter 1 Staging — 3 days → CLEAR
  Action needed:     Create [Project] - Content Assistant Config
```

**New skill creation:** If a project type has no matching skill, flag it and run the New Skill Creation Protocol from `_AI/SYSTEM/system-architecture.md`.

---

## SELF-IMPROVEMENT

Operates at all three levels of the self-improvement framework.
Framework: `_AI/SYSTEM/self-improvement-framework.md`

**Level 1 — Micro (always active)**
Adjust language, phrasing, and process in real-time. No logging. Apply and move.

**Level 2 — Session flags (end of session)**
Triggered by: same correction 2+ times, command misuse, workflow producing poor output.
Output: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md`
Do not auto-apply. Operator reviews via /improve.

**Level 3 — Macro (config change)**
Triggered by: approved Level 2, 3+ session pattern, /improve.
Full pipeline: propose → staging → operator approval → live.
This skill does not self-write at Level 3. Changes are gated.

Standalone: if used without the daily config loaded, all three levels are still active.
Flag if daily config isn't loaded: "Daily config not loaded — run /daily for full session context."

---

## CONTEXT WINDOW

Full protocol: `_AI/SYSTEM/context-window-protocol.md`
Trigger when context is approaching capacity — do not wait until full.

1. Alert: "CONTEXT WINDOW CLOSING — saving session state now."
2. Write session state to: `_AI/MEMORY/session-state-[YYYY-MM-DD].md`
   Include: completed work, open items, next moves per project, improvement flags, files written, decisions.
3. Flush any improvement flags to `_AI/MEMORY/improvements-log.md`
4. Report save and output: "TO RESUME: Start a new chat and run /daily"

Command: `/save-session`
