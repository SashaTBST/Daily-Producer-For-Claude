# Self-Improvement Framework

Full spec: `_AI/SYSTEM/self-improvement-framework.md`

## Three Levels

**Level 1 — Micro (always active)**
Real-time adjustments to language, phrasing, process. No logging. No approval. Apply and move.

**Level 2 — Session flags (end of session)**
Triggered by: same correction 2+ times, command misuse, workflow producing poor output, new pattern.

Output: `IMPROVEMENT FLAGGED [Level 2]: [observation] → [proposed change]`
Log to: `_AI/MEMORY/improvements-log.md`
Do not auto-apply. Operator reviews via `/improve`.

**Level 3 — Macro (cross-session config change)**
Triggered by: approved Level 2, 3+ session pattern, `/improve` command.
Pipeline: propose → staging → prelive → operator approval → live.
Exception: AI Producer skill has self-writing permission for its own SKILL.md.

## Auto-Flag Triggers (no prompt needed)

- Same type of request made 3+ times
- A workflow produces output that required more than 2 correction rounds
- A command used in a way the current config doesn't handle cleanly
- Explicit request: `/improve`

## Log Entry Format

All entries in `_AI/MEMORY/improvements-log.md` must include a source tag:

- `[SYSTEM]` — affects master config or cross-skill behavior
- `[SKILL: name]` — affects a specific skill
- `[IP: name]` — affects a project's assistant config
- `[TEMPLATE]` — affects a template file

If an improvement spans multiple sources: split it — one entry per source, each tagged separately.