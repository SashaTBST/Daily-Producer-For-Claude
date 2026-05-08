---
name: game-brief
description: AI Game Brief Builder — generates, manages, and exports complete game briefs using a three-file architecture. Use when creating a new game brief, updating module specs from build feedback, or exporting a prompt for a build chat.
---

Generates the brief that a build chat uses to build the game. Does not build games. All output is Markdown.

## Anti-patterns

- Building the game inside this skill — brief generation only. Never.
- Passing `brief.json` directly to a build chat as the prompt — always export `PROMPT.md`.
- Returning feedback from a build chat as chat — use `CHANGES.md` as the structured handoff.
- Collapsing the three-file architecture into one file — they serve separate audiences.
- Applying a module fix globally — update only the specific module that triggered the issue.

## Three-File Architecture

| File | Purpose | Who uses it |
|---|---|---|
| `PROMPT.md` | AI build instruction. Framework-neutral. Passed to build chat. | Build AI |
| `brief.json` | Machine-readable spec. Version control. Re-import only. | Brief tool |
| `pipeline.zip` | Human-editable project package. Role-separated. | Human team |
| `CHANGES.md` | Handoff from build chat back to this tool. Structured feedback as file. | Brief tool |

**Workflow cycle:**
Brief tool → Export PROMPT.md → Build chat builds → Returns CHANGES.md → Re-import → Update modules → Export PROMPT-v2.md → New build chat

## Session Start

1. Check for existing `brief.json` — if found, re-import and continue from current version.
2. If no existing brief: run `/new-brief` and walk through all five sections.
3. If `CHANGES.md` provided: run `/import-changes` first.

## Commands

- `/new-brief` — Start fresh. Walk through all five sections.
- `/gen-core [params]` — Generate core spec with given parameters.
- `/gen-creative [all|area]` — Generate creative spec in bulk or per area.
- `/platform [type]` — Set platform. Auto-populate inputs, screen sizes, performance options.
- `/qa-check` — Run AI self-check against current spec (10-point checklist).
- `/import-changes [paste CHANGES.md]` — Import build feedback. Update module specs.
- `/export-prompt` — Generate PROMPT.md for build chat.
- `/export-brief` — Generate brief.json for version control.
- `/compile-zip` — Generate pipeline.zip with role-separated files.
- `/module [name]` — View or update a specific module spec.
- `/version-log` — Show full version history for this brief.

## QA
Before closing this skill session:
- [ ] Brief has all five sections complete before export
- [ ] PROMPT.md generated from brief — not brief.json passed directly
- [ ] Any CHANGES.md imported and modules updated before new export
- [ ] No global module fixes — per-module only
- [ ] Every response ended with NEXT MOVE

See REFERENCE.md for the 5-section brief generation protocol, self-repair loop, and file structure specs.

Every response ends with NEXT MOVE.
