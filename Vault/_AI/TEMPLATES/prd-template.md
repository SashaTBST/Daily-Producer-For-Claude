---
type: PRD
status: Draft
version: 0.1
owner:
created: {{date}}
last_updated: {{date}}
tags: [prd, product]
---

# Product Requirements Document

---

# 0. Executive Snapshot

## One-Sentence Summary
> Describe the transformation this product creates (not the feature set).

## Who It Is For
- Primary user:
- Context of use:
- Maturity level:

## Core Outcome
> What measurable change should occur?

## Explicitly Not Included
-
-
-

---

# 1. Problem Definition

## 1.1 User Segment

- Who specifically?
- What environment do they operate in?
- What tools/processes are they using today?

## 1.2 Job To Be Done

> When ________, the user wants to ________, so they can ________.

## 1.3 Current Friction

- What is broken?
- What workaround exists?
- What inefficiency exists?

## 1.4 Evidence

- Observational data:
- Quantitative data:
- Anecdotal feedback:
- Assumptions (explicitly list):

---

# 2. Outcome Definition

## 2.1 Success Metrics

### Behavioural Metrics
-

### Business Metrics
-

### Quality Metrics
-

## 2.2 Kill Criteria

> Under what measurable condition do we stop?

---

# 3. Hypothesis Framing

> If we [intervention], then [user] will [behaviour change], resulting in [measurable impact].

### Supporting Assumptions
-
-

### What Would Falsify This?
-

---

# 4. Scope Definition

## 4.1 In Scope
-
-
-

## 4.2 Out of Scope
-
-
-

## 4.3 Constraints

### Budget
### Timeline
### Regulatory
### Platform
### Team Capability

---

# 5. Functional Requirements

Describe system behaviour, not UI.

## 5.1 Core Flow

Trigger → Action → System Response

1.
2.
3.

## 5.2 Edge Cases

- Invalid input:
- No input:
- Permission failure:
- System failure:

---

# 6. Non-Functional Requirements

## Performance
-

## Security
-

## Logging & Observability
-

## Accessibility
-

## Scalability
-

## Maintainability
-

---

# 7. Dependencies & Assumptions

## 7.1 External Dependencies
-

## 7.2 Internal Dependencies
-

## 7.3 Assumption Register

| Assumption | Risk Level | Validation Plan | Status |
|------------|------------|----------------|--------|
|            |            |                |        |

---

# 8. Ralph Wiggum Loop Plan

Break into smallest testable cycles.

---

## Loop 1

### Objective
### Hypothesis
### Smallest Testable Build
### Measurement Method
### Success Threshold
### Kill Threshold
### Observations
### Decision (Continue / Refine / Kill)

---

## Loop 2

### Objective
### Hypothesis
### Smallest Testable Build
### Measurement Method
### Success Threshold
### Kill Threshold
### Observations
### Decision

---

## Loop 3

### Objective
### Hypothesis
### Smallest Testable Build
### Measurement Method
### Success Threshold
### Kill Threshold
### Observations
### Decision

---

# 9. Instrumentation Plan

## Events to Track
-
-

## Data Storage Location
-

## Reporting Owner
-

## Review Cadence
-

---

# 10. Risk Register (Pre-Mortem)

> If this fails in 6 months, why?

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
|      |            |        |            |

---

# 11. Definition of Done

- [ ] Functional requirements implemented
- [ ] Edge cases handled
- [ ] Metrics tracking live
- [ ] Documentation written
- [ ] Owner assigned
- [ ] Rollback plan documented
- [ ] Post-loop review completed

---

# 12. AI Collaboration Prompts

Use these when working with AI.

## Clarification
- "Ask me clarifying questions before structuring this."

## Problem Extraction
- "Remove solution bias and restate the user problem."

## Hypothesis
- "Rewrite this as a falsifiable hypothesis with measurable behavioural change."

## Scope Reduction
- "Reduce this to the smallest build that validates the hypothesis."

## Risk Analysis
- "List behavioural, operational, and economic failure modes."

## Loop Design
- "Break this into 3 validation loops with measurable gates."

---

# 13. Version Log

| Version | Date | Author | Summary of Changes |
|---------|------|--------|-------------------|
| 0.1     |      |        | Initial draft     |
