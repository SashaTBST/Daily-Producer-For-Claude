# Triage Issue — Reference

## Phase Classification Matrix

| Signal | Phase | Next action |
|---|---|---|
| Vague idea, no detail | Idea | Run /grill-me |
| Hypothesis untested | Prototype | Build spike first |
| No PRD exists | PRD | Run /prd |
| PRD exists, no plan | Plan | Run /prd-to-plan |
| Plan exists, ready to build | Execute | TDD or direct impl |
| Behaviour needs verifying | QA | Write acceptance test |

## HITL vs AFK

**HITL (Human In The Loop) — requires human at decision point**
- Architectural decision required
- Creative direction input needed
- External dependency (client approval, API key, legal)
- Acceptance criteria are ambiguous

**AFK (Autonomous) — can run without human input**
- Acceptance criteria are unambiguous and specific
- No architectural decisions required
- All dependencies resolved
- Tests can verify the outcome objectively

## Acceptance Criteria Template

```
Given [context or precondition]
When [action or trigger]
Then [observable outcome]
```

**Good:** "Given a logged-out user, when they visit /dashboard, then they are redirected to /login."
**Bad:** "It should work correctly." / "It should be fast." / "It should look good."

## Type Definitions

- **Bug** — existing behaviour doesn't match specification
- **Feature** — new behaviour being added
- **Chore** — internal work with no user-facing change (deps, refactor, infra)
- **Docs** — documentation only
- **Architecture** — structural change that enables future work