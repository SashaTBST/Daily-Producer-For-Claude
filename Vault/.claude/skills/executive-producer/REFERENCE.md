# Executive Producer — Reference

## Config Creation Protocol (13 questions)

Run when a project has no Producer Assistant Config. Ask one at a time — do not skip.

1. What is this project? (name, type: novel / game / business / brand / tool — one sentence)
2. What is the end deliverable? (what does done look like — what exists that didn't before)
3. What is the primary goal and who is the target audience?
4. What does success look like in 90 days? (specific metric or observable outcome)
5. What is the current stage? (idea / brief / in production / near launch / live)
6. What is the timeline? (hard deadline or target — be specific)
7. What is the biggest risk? (what could derail this project)
8. What dependencies exist? (what must be in place before this can move forward)
9. What skills are needed? (writer / game-maker / strategy / tdd / prd etc)
10. What is the source of truth, and does it exist? (type + file location if yes)
11. What are the non-negotiables? (brand rules, IP constraints, compliance, quality standard)
12. What has already been decided (locked) and what is still open?
13. What does the operator need from this session specifically?

Output: `[Project]/Working Files/AI/[Project] - Producer Assistant Config.md` — staging first, pipeline applies.

---

## EP Top-Line Session Format

Token budget per session start: under 150 tokens on project status.

Format — one line per project, dense:
```
[Project] — staging: [what's in staging] — next: [one action]
[Project] — blocked: [blocker] — waiting: [what's needed]
[Project] — on hold — resume trigger: [condition]
```

Only surface projects with active state or pending action. Omit on-hold projects unless they have a trigger condition due.

---

## PM Role — Invocation Reference

EP invokes PM when ALL of these are true:
- Project name identified from arguments or operator
- No active build plan exists for this project (or it's a new project)
- Session is a project-start, not a mid-build continuation

EP does NOT invoke PM when:
- An unchecked plan item exists (resume there instead)
- Operator gives a specific action directly
- Context window is constrained (flag and offer PM as a separate session)

---

## Request Classifier — Source Tag Reference

| Request type | Source tag | Portable sync | Gate |
|---|---|---|---|
| Affects rules / build protocol / config | [SYSTEM] | Required (non-IP) | L2 flag or L3 build protocol |
| Affects a specific skill | [SKILL: name] | Required (non-IP) | L2 flag or L3 build protocol |
| Affects a project assistant config | [IP: project] | Local only | L2 flag or L3 build protocol |
| Affects a template | [TEMPLATE] | Required (non-IP) | L2 flag or L3 build protocol |
| Spans multiple sources | Split entries | Per source | Per source |

L1 improvements: apply in-session, no log, no sync.
L2 improvements: log to improvements-log before proceeding. Operator reviews via /improve.
L3 improvements: full build protocol. Do not skip phases.

---

## Migration from /ai-producer

Existing Producer Assistant Configs: no changes needed — they remain valid at Layer 2.
If an existing config references `/ai-producer`: it redirects automatically via stub.
New configs: use `/executive-producer` in all references.
