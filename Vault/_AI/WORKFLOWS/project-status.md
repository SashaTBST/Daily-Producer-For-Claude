◆ WORKFLOW: PROJECT STATUS ◆
Load via: /load _AI/WORKFLOWS/project-status
Layer on top of daily-ai-config for deep project review.

---

PURPOSE

This workflow produces a full status report on one or all active projects. It surfaces progress, blockers, decisions pending, risks, and next actions. It does not produce creative output — it produces clarity.

Use when: beginning a project session, preparing for a meeting, reviewing progress before a decision, or at end of week.

---

STATUS REPORT PROTOCOL

INPUT REQUIRED: [Project name] OR [all] for full sweep.

STEP 1 — LOCATE ACTIVE FILES
Reference the project's staging, prelive, and live files. Note which stage each element is currently in.

STEP 2 — PROGRESS ASSESSMENT
What has been completed since last review?
What is currently in progress?
What has not been started that should have been?

STEP 3 — BLOCKER IDENTIFICATION
What is blocking progress?
Is the blocker internal (decision needed) or external (dependency, resource, information)?
What is needed to unblock it?

STEP 4 — RISK SURFACE
What assumptions is this project currently making?
What would break it if those assumptions are wrong?
What is the highest-risk element right now?

STEP 5 — DECISION LOG CHECK
What decisions are pending?
What decisions have been made that are not yet reflected in working files?

STEP 6 — NEXT ACTIONS
List the next 3-5 actions needed to move the project forward.
Each action: what it is, who owns it, what it unlocks.

---

OUTPUT FORMAT

## PROJECT STATUS — [PROJECT NAME] — [DATE]

### PIPELINE STATE
| Element | Staging | Prelive | Live |
|---------|---------|---------|------|
| [file]  | [Y/N]   | [Y/N]   | [Y/N] |

### PROGRESS
Done: [bullet list]
In Progress: [bullet list]
Behind: [bullet list — with reason]

### BLOCKERS
[Blocker — type: internal/external — what's needed to clear it]

### RISKS
[Risk — likelihood — impact — mitigation]

### PENDING DECISIONS
[Decision — who decides — what it blocks]

### NEXT ACTIONS
1. [Action — owner — unlocks]
2. [Action — owner — unlocks]
3. [Action — owner — unlocks]

---

END PROJECT STATUS WORKFLOW
