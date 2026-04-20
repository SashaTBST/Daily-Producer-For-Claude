# Request Refactor Plan — Reference

## Plan Template

Use this for both GitHub issues and Markdown plan files. Do not include specific file paths or code snippets — they go stale.

---

### Problem Statement

The problem the developer is facing, from the developer's perspective. What is broken, inefficient, or wrong about the current state?

### Solution

The proposed solution. What will be different after this refactor? What does success look like?

### Commits

A long, detailed implementation plan. Each commit must:
- Leave the codebase in a working state
- Be as small as possible (Martin Fowler: "make each refactoring step as small as possible")
- Represent one logical change

Format each commit as:
```
[commit N] type(scope): description
What this commit does and why it's a safe stopping point.
```

### Decision Document

Implementation decisions made during the interview:
- Modules being built or modified
- Interface changes
- Technical clarifications from the developer
- Architectural decisions
- Schema or API contract changes
- Specific interactions between components

### Testing Decisions

- What makes a good test for this area (test external behaviour, not implementation details)
- Which modules will be tested
- Prior art in the codebase (similar tests to reference)
- Any areas where test coverage is insufficient and the plan to address it

### Out of Scope

Explicit list of what this refactor does NOT change. This is as important as what it does change — it defines the blast radius.

### Further Notes (optional)

Any additional context, risks, or observations from the exploration phase.

---

## Dual-Mode Filing

**GitHub-enabled project:**
```
gh issue create --title "refactor: [short description]" --body "$(cat <<'EOF'
[paste plan template content]
EOF
)"
```

**Markdown-only project:**
File to `plans/[feature]-refactor.md` using the plan template above.
Follow standard pipeline from there: Staging → Prelive → Live when approved.
