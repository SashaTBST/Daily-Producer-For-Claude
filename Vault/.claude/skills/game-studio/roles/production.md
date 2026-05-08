---
name: production
parent: game-studio
role: Spec role — owns schedule, milestones, resource plan, risk register, output routing config
restrictions:
  - Spec role — runs once, does not loop
  - Does not make creative decisions (defers to director)
  - Does not manage external stakeholders (operator handles external comms)
  - Console platform: flags cert timeline and manual gate explicitly — does not schedule cert
---

## Inputs
Read: Game Assistant Config — team, launch target, budget status, platform, scope
Read: All Phase 1 spec handoffs when available

## What This Role Produces
- Phase breakdown: pre-production → production → alpha → beta → gold → launch with entry/exit criteria
- Milestone schedule: mapped to launch target with buffer recommendations
- Task breakdown: per role, per phase — ownership and dependencies
- Risk register: top risks ranked by likelihood × impact with mitigations
- Scope cut list: ranked cut candidates if timeline slips
- **Output routing config:** local build path, render pipeline path, GitHub repo and branch strategy

## Output Routing Config (required output)
```
[project]/production-config.md

build_output_path: [local absolute path]/builds/[platform]/
render_output_path: [local absolute path]/render/
github_repo: [repo URL]
github_branch_strategy: feature/game-studio-[YYYY-MM-DD]
commit_format: feat([role]): [output] — studio loop pass [N]
```
This config is read by the director for Phase 4 output routing.

## Pass Condition (spec role — auto-pass)
Phase breakdown written. Risk register has at least 3 entries. Output routing config written and paths confirmed valid on local machine.

## Handoff Output
```
[project]/handoffs/production-handoff.md
Docs: [phase-breakdown path, risk-register path, production-config path]
For all roles: scope constraints and timeline flagged
For qa: phase gate criteria confirmed
For director: output routing config path confirmed
```
