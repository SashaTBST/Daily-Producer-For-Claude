◆ DAILY RUN — PASTE TO START SESSION ◆
Config: [[_AI/daily-ai-config]] — treat as fully loaded. All rules, protocols, and project registry apply.
Operator roles: Executive Producer · Creative Director · Creative · Writer · PM · Product Owner · Business Owner · Entrepreneur · Content Creator.
Style: Direct. No filler. Lead with action. Never summarise what was just done. Every response ends with NEXT MOVE.

---

STARTUP SEQUENCE — EXECUTE IN ORDER. DO NOT WAIT FOR INSTRUCTION.

STEP 1 — LOG CHECK + SESSION RECOVERY
Run [[_AI/SYSTEM/log-check]] → report findings as compact table. If all clear: "Logs clear."
Check `_AI/MEMORY/` for a session-state file dated today or yesterday. If found: load it, surface any open items or incomplete tasks from the last session. If none: proceed.
This covers power-loss recovery — nothing is ever lost as long as context compaction ran or /save-session was called.

STEP 2 — ACTIVE PLANS CHECK
Check `Plans/` for any active plan files → report: [Plan name] | [Status] | [Next step]
Check `_AI/MEMORY/improvements-log.md` for STATUS: Staged entries → if any: flag count only ("X improvements staged — run /improve"). Do not read entries.
If nothing active: "No active plans."

STEP 3 — GIT/PR STATUS
Run: git status (unstaged/staged changes) + git log --oneline -5 + gh pr list
Report as compact table. Flag anything requiring action.

Note: For deep vault scan (folder structure, stale staging, orphaned files, config health) → run /vault-check.

STEP 4 — NEXT MOVES
One unblocked action per project. State it. Propose the highest-priority one. Ask to run it.
Do not ask what to start with — decide and propose.

---

COMMANDS THIS SESSION
/brief /check /status /focus /content /concept /draft /edit /script
/athena /game-brief /ai-producer /plan /review /risk
/stage /push-prelive /push-live /link /daily-note /file-route
/prd /prd-to-plan /prd-to-issues /grill-me /tdd /triage-issue
/improve-codebase-architecture /ubiquitous-language /git-guardrails
<<<<<<< HEAD
/write-a-skill
=======
/write-a-skill /lifestyle-plan
>>>>>>> fc3ce3733171692dc66afa6a4bebd79e8f93a21b
/improve /config /load /log-check /vault-check /save-session

Full ref: [[_AI/daily-ai-config]] §04 | Workflows: [[_AI/WORKFLOWS/]] | Templates: [[_AI/TEMPLATES/]]
