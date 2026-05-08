---
name: studio-ops
description: Studio-level operations for a game studio â€” cross-project status tracking, studio roadmap, go-to-market planning per game title, and release schedule management across all active projects. Use when planning which games ship in what order, tracking multi-project studio state, building a GTM plan for a specific title, or managing studio-wide milestones. NOT a production execution skill â€” for building a game use /game-studio. NOT a content/acquisition skill â€” use /strategy.
argument-hint: "[mode: status|roadmap|gtm|track] [project name if applicable]"
---

## Scope

/studio-ops owns: cross-project status sweeps, studio-wide release roadmap, go-to-market plans per game title (Steam page, press kit, launch sequence), milestone tracking, and resource priority decisions that span multiple projects. IP separation applies â€” no game lore, only project metadata and schedules.

Routes out:
- Single-game production execution â†’ `/game-studio`
- Game brief or GDD generation â†’ `/game-maker`
- Brand acquisition/content strategy â†’ `/strategy`
- Cross-domain all-projects daily driver â†’ `/executive-producer`

## Session Start

1. Load project registry from `_AI/daily-ai-config.md` â€” game projects only
2. Surface any game project with no status update in 14+ days â†’ flag for STATUS sweep
3. Identify mode from `$ARGUMENTS` or ask: "Status sweep, roadmap update, GTM for a specific game, or milestone tracking?"
4. Propose single next unblocked studio-level action

## Modes

**STATUS** â€” Game-projects-only sweep. One line per active game project:
`[Game] â€” [stage] â€” [pipeline state] â€” next: [action] / blocked: [blocker]`
Stages: Idea â†’ GDD â†’ Pre-production â†’ Production â†’ GTM â†’ Launch â†’ Post-launch

**ROADMAP** â€” Build or update the studio-wide release roadmap. Which games ship, in what order, targeting what window. Outputs to: `HatchFox Studios - Business/Working Files/Studio - Roadmap-Staging.md`

**GTM** â€” Go-to-market plan for a named game title. Loads that game's GDD/Source of Truth. Covers: Steam page, press kit, store assets, key art, launch sequence, social blast, review copy schedule, launch-day checklist. Outputs to: `[Game Brand]/Working Files/Strategy/[Game] - GTM-Staging.md`

**TRACK** â€” Milestone tracking and release schedule. Named project or all active. Outputs to: `HatchFox Studios - Business/Working Files/Studio - ReleaseSchedule-Staging.md`

Full output templates â†’ REFERENCE.md.

## Studio Hierarchy â€” Where This Fits

```
/executive-producer   â† all-domain daily driver (writing, content, TBST, games)
  /studio-ops         â† game studio operations layer (this skill)
    /game-studio      â† single-game production execution
    /game-maker       â† brief/GDD generation (usable standalone)
    /strategy         â† brand acquisition + content per game brand
```

/studio-ops escalates to /executive-producer for cross-domain decisions (budget, staffing, non-game priorities).

## Cross-Skill Integration

- `/game-maker` â€” source of briefs and GDDs; studio-ops reads their output, doesn't generate them
- `/game-studio` â€” source of production state; studio-ops surfaces it, doesn't run it
- `/strategy` â€” owns content/acquisition per brand; GTM mode hands off social/content to /strategy after launch plan is built
- `/executive-producer` â€” escalation path for cross-domain studio decisions

## Anti-patterns

âœ— Never generate game lore, characters, or world detail â€” IP belongs in project files only
âœ— Never run production execution â€” route to /game-studio
âœ— Never duplicate /executive-producer top-line â€” STATUS mode is game-specific only
âœ— Never load all project GDDs at session start â€” load on demand per mode/project
âœ— Never write GTM content to live without prelive review â€” Steam copy and press copy require operator approval
âœ— Never make resource priority decisions without surfacing tradeoffs to operator first

## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.