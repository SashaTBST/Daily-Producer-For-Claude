◆ WORKFLOW: DAILY BRIEF ◆
Load via: /load _AI/WORKFLOWS/daily-brief
Layer on top of daily-ai-config for morning session setup.

---

PURPOSE

This workflow sets up the working day. It establishes focus, surfaces active priorities across all projects, and identifies the single most important output needed today.

Run this at the start of any session. Combine with today's date and any user input about current context.

---

BRIEF GENERATION PROTOCOL

When this workflow is loaded, execute the following in sequence:

STEP 1 — DATE AND CONTEXT
State today's date. Ask if there is anything new since the last session that needs to be loaded (decisions made, context changes, new inputs).

STEP 2 — PROJECT SWEEP
For each active project in the registry, surface:
→  What is the current status?
→  What is the next milestone?
→  Is there a blocker?
→  Is there an overdue action?

Report each project in one tight block. Flag anything that needs a decision today.

STEP 3 — PRIORITY STACK
Based on the sweep, propose the priority stack for the day:
1.  The one thing that must get done today (deadline or dependency)
2.  The one thing that will move the most value if done today
3.  The things that can wait but should not be forgotten

STEP 4 — ROLE LENS
Given today's priority stack, identify which roles are leading today:
→  E.g. "Today is primarily PM + Creative Director. Writer mode available in the afternoon."

STEP 5 — OPEN QUESTION FLAG
List any open questions from active project files that are currently blocking work.

---

OUTPUT FORMAT

## DAILY BRIEF — [DATE]

### PROJECT STATUS
**[Project Name]**
Status: [one line]
Next: [next milestone or action]
Blocker: [if any — or CLEAR]

### TODAY'S PRIORITY STACK
1. [Must do — reason]
2. [High value — reason]
3. [Watch list]

### ACTIVE ROLES TODAY
[Roles leading — roles available]

### OPEN QUESTIONS REQUIRING DECISION
- [Question — which project — blocking what]

---

END DAILY BRIEF WORKFLOW
Combine with daily-ai-config for full context.
