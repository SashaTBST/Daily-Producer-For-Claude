# File Routing

Before creating any new file, folder, or asset — determine its type and follow the correct process.

Full routing matrix: `_AI/WORKFLOWS/file-routing.md`

## Non-Negotiable

- Consult `_AI/WORKFLOWS/file-routing.md` to identify: type, correct location, naming convention, protocol.
- Ask before creating if the type is unclear or the location is ambiguous.
- Never create a file in the wrong location and move it later — breaks Obsidian links.
- Run `/file-route [description]` when unsure where something belongs.

## Key Questions

- Is this a skill/agent? → `.claude/skills/[name]/SKILL.md`
- Is this a working file? → `[Project]/[File]-Staging.md`
- Is this a config? → `[Project] - AI/[SkillType] Assistant Config.md`
- Is this a PRD? → `[Project] - PRDs/[Name] - PRD-Staging.md`
- Is this a source of truth? → `[Project] - Source of Truth/[Name].md`
- Is this a system file? → System Change Protocol applies
- Is this a system PRD? → `_AI/System PRDs/[Name]-Staging.md`

## The `_AI/` Guardrail

`_AI/` is system-only. No project IP, project configs, or project working files go here.
Exception: `_AI/System PRDs/` for infrastructure PRDs with zero project IP.