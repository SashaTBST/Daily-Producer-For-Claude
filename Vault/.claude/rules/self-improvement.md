# Self-Improvement Protocol

Full framework: `_AI/SYSTEM/self-improvement-framework.md`

## Improvement Levels

**Level 1 — Micro (in-session)**
Real-time adjustments to language, phrasing, process. No logging. No approval. Apply and move.
Active at all times.

**Level 2 — Session flag**
Triggered by: same correction 2+ times, command misuse, workflow producing poor output, new pattern.
Output at point of detection: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md`. Do not auto-apply. Operator reviews via /improve.

**Level 3 — Macro (config change)**
Triggered by: approved Level 2, 3+ session pattern, /improve command.
Full pipeline: propose → staging → prelive → operator approval → live.
Exception: AI Producer skill is self-writing — applies directly to its own command file, logs the change, flags to operator in same response.

## Auto-Flag Conditions (without being asked)

- Same type of request made 3+ times in a session
- Workflow produces output requiring more than 2 correction rounds
- Command used in a way the current config doesn't handle cleanly
- Explicit /improve request

## Improvements Log Format

Every entry in `_AI/MEMORY/improvements-log.md` requires a source tag on the TRIGGER line:

- `[SYSTEM]` — affects master config or cross-skill behaviour
- `[SKILL: name]` — affects a specific skill
- `[IP: name]` — affects a project assistant config
- `[SKILL: name + IP: name]` — spans both
- `[TEMPLATE]` — affects a template file

If an improvement spans multiple sources: split it — one entry per source, each tagged separately.
