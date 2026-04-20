# [PROJECT NAME] — PRODUCER ASSISTANT CONFIG
Project: [Project or client name]
Type: [What kind of work — product build, client deliverable, internal initiative, etc.]
Assistant type: Producer Partner
Version: 1.0
Last updated: [YYYY-MM-DD]

This file defines HOW to produce this project — not what it is.
Project details, deliverables, and scope live in the project brief or strategy doc.
The producer skill's base behavior (prioritisation, risk, driving forward) lives in the /ai-producer skill.
This file tells the producer tool how to apply its behavior specifically to THIS project.

Created by: Producer assistant (Producer Config Creation Protocol)
Maintained by: Producer assistant adds to this file when new project-specific producing rules are established.

---

## HOW THIS FILE IS USED

The /ai-producer skill reads this file when working on [PROJECT NAME].
It applies every rule here ON TOP OF its base producing behavior.
If a rule here conflicts with base behavior, this file wins.

Project brief / strategy: `[path to source of truth]`
Working files: `[path to staging folder]`

---

## 01. WHAT THIS PROJECT IS TRYING TO DO

GOAL — ONE SENTENCE
[The single outcome that defines success for this project]

SUCCESS LOOKS LIKE
→ [Specific outcome 1 that would confirm success]
→ [Specific outcome 2]

FAILURE LOOKS LIKE
✗ [Specific outcome that would confirm failure]
✗ [What must not happen]

---

## 02. CONSTRAINTS — LOCKED

TIMELINE
[Hard deadline or target date, if any. What is flexible and what is not.]

BUDGET / RESOURCE
[Any constraints on cost, time, or headcount]

DEPENDENCIES
[What this project depends on that is outside direct control]

BLOCKERS TO WATCH
[Known risks or dependencies that could stall progress — check these proactively]

---

## 03. DECISION AUTHORITY

WHO DECIDES WHAT
[Who has authority over which decisions. This prevents the producer asking for unnecessary approval.]
→ Operator decides: [types of decisions]
→ Producer proposes: [types of decisions]
→ Escalate before acting: [situations that require explicit confirmation]

APPROVAL GATES
[Any points where explicit approval is required before proceeding]
→ Before: [specific action] → confirm with operator first
→ Before: [specific action] → confirm with operator first

---

## 04. COMMUNICATION RULES FOR THIS PROJECT

HOW UPDATES SHOULD LAND
[How does the operator prefer to receive progress reports for this project? Detail / summary / exception-only?]

CADENCE
[How often should the producer check in or report?]

WHAT TO FLAG IMMEDIATELY
→ [Situation that always warrants immediate flagging]
→ [Situation]

WHAT NOT TO FLAG UNTIL RESOLVED
→ [Type of thing to handle and not escalate unless it becomes a blocker]

---

## 05. PRODUCING STYLE FOR THIS PROJECT

Every project has a different pace and rhythm. Define it here.

PACE
[Aggressive (ship fast, iterate) / Deliberate (quality gates, careful) / Flexible — describe]

RISK TOLERANCE
[High (move fast, accept mistakes) / Low (validate before proceeding) / Specific areas to treat differently]

WHERE AI LEADS
→ [Areas where the producer drives without needing confirmation on each step]

WHERE AI PROPOSES AND WAITS
→ [Areas where the producer must present options and wait for selection]

---

## 06. OPEN PRODUCING QUESTIONS

Questions that affect how this project is managed. The producer asks these proactively.

HIGH — BLOCKS PROGRESS
◻ [Question 1]

MEDIUM
◻ [Question 1]

RESOLVED
◈ [YYYY-MM-DD] [Question]: [Answer locked]

---

## 07. HOW THIS FILE GROWS

The producer tool adds to this file when:
- A producing decision is made that affects all future sessions on this project
- A communication preference is confirmed
- A constraint is locked
- A decision authority boundary is clarified

End-of-session prompt: "New producing rule identified: [rule]. Add to Producer Assistant Config? Yes / No"

---

## 08. QUESTIONS THIS FILE WAS BUILT FROM

ANSWERED
✅ [Question] → [Answer]

OPEN
◻ [Question not yet answered]

---

[PROJECT NAME] — PRODUCER ASSISTANT CONFIG v1.0
Project brief: [[path to source of truth]]
Skill: /ai-producer (reads this file when project is active)
