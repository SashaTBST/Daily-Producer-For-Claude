---
name: qa
description: Interactive QA session where user reports bugs or issues conversationally and the agent files them as GitHub issues or Markdown staging entries. Explores context in the background. Use when user wants to report bugs, run QA, file issues conversationally, or says "QA session".
---

# QA Session

Run an interactive QA session. User describes problems. You clarify, explore for context, and file durable issues.

**At session start — ask:** "Is this project GitHub-enabled or Markdown-only?"
- **GitHub** → file with `gh issue create`
- **Markdown** → append to `[project]-Issues-Staging.md` in the project folder

## For each issue the user raises

### 1. Listen and lightly clarify

Let the user describe the problem. Ask at most 2–3 short questions:
- What they expected vs what actually happened
- Steps to reproduce (if not obvious)
- Consistent or intermittent?

Do NOT over-interview. If the description is clear — move on.

### 2. Explore in the background

While talking, spawn an Explore subagent to understand the relevant area. Goal: learn domain language and what the feature is supposed to do. NOT to find a fix. Do not reference file paths or line numbers in the filed issue.

### 3. Single issue or breakdown?

Break down when: fix spans multiple independent areas, or there are clearly separable concerns. Keep as one when: one behavior is wrong in one place.

### 4. File the issue

See REFERENCE.md for issue templates.

Rules for all issues:
- No file paths or line numbers — they go stale
- Use the project's domain language
- Describe behaviours, not code
- Reproduction steps are mandatory
- Keep it concise — readable in 30 seconds

After filing, print the URL (GitHub) or confirm the staging entry, then ask: "Next issue, or done?"

### 5. Continue

Each issue is independent. Keep going until user says done.
