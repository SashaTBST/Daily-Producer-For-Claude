---
name: lifestyle-plan
description: Run a weekly check-in or update the personal lifestyle plan — tracks progress toward 115kg → 90kg goal. Use every Monday for the weekly weigh-in, or any time to adjust the plan, log a plateau, or escalate the exercise phase.
---

**Source of Truth:** `Sasha - Person/Lifestyle/Sasha - LifestylePlan.md`
**Staging:** `Sasha - Person/Lifestyle/Working Files/Staging/Sasha - LifestylePlan-Staging.md`

Load the live plan before any session. Confirm it is current.

## Anti-patterns

- Writing directly to the live plan — all changes go to staging first.
- Overhauling the whole plan on a plateau — one variable change only, ever.
- Skipping the physio assessment before Phase 3 upper body work — not optional, regardless of motivation.
- Asking all 5 check-in questions at once — one at a time, in order.

## Session Types

**Weekly check-in (Monday):**
Ask in order — one question at a time:
1. Weight this morning?
2. Walks this week — how many?
3. Breakfast every day?
4. Streak score — how many days hit all 4 habits (breakfast, walk, slow dinner, kitchen closed)?
5. What made it hard this week?

After all 5: log to progress tracker in staging. Assess against milestone map. Flag if plateau rule triggers.

**Plateau protocol:**
3 consecutive weeks at same weight → propose one variable change. Options: reduce evening snack, add a walk, adjust lunch portion, add a circuit session.

**Phase progression check:**
- Phase 2 unlocks: 3+ weeks of 4+/week walking → add bodyweight circuits
- Phase 3 unlocks: 8+ consistent weeks → gym, but get shoulder assessed by physio first

**Plan adjustment:**
Name what isn't working, why, and what one change to make. Stage the change — do not overwrite live.

## Rules

- Left shoulder: no overhead press, lateral raises, pull-ups, or upright rows — ever
- Physio assessment required before Phase 3 upper body work — not optional
- Takeaway 1x/week (Saturday) is built in — no guilt, no adjustment unless very large meal
- Weekly weigh-in: Monday morning, before eating
- One variable at a time on plateau — never overhaul the whole plan at once

## Milestone Map

| Milestone | Weight | Status |
|-----------|--------|--------|
| Start | 115kg | 2026-03-24 |
| 1 | 110kg | 🎯 Current target |
| 2 | 105kg | Phase 2 workouts |
| 3 | 100kg | Major landmark |
| 4 | 95kg | Phase 3 / gym |
| 5 | 90kg | Minimum goal |

## QA
Before closing this skill session:
- [ ] Live plan loaded and confirmed before any changes
- [ ] All check-in data logged to staging (not live)
- [ ] If plateau: one variable change only
- [ ] Phase 3 gated behind physio assessment
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
