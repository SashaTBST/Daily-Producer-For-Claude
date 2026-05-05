# /request-refactor-plan — Reference

## Plan Template

Use for both GitHub issues and Markdown plan files.

**Problem Statement**
The problem from the developer's perspective. What is broken, inefficient, or wrong?

**Solution**
What will be different after this refactor? What does success look like?

**Commits**
Each commit must:
- Leave the codebase in a working state
- Be as small as possible
- Represent one logical change

Format:
```
[commit N] type(scope): description
What this commit does and why it's a safe stopping point.
```

**Decision Document**
- Modules being built or modified
- Interface changes
- Technical clarifications
- Architectural decisions
- Schema or API contract changes

**Testing Decisions**
- What makes a good test for this area
- Which modules will be tested
- Prior art in the codebase
- Areas with insufficient coverage and the plan to address it

**Out of Scope**
Explicit list of what this refactor does NOT change.

**Further Notes (optional)**
Additional context, risks, or observations.

## Filing

**GitHub:**
```
gh issue create --title "refactor: [short description]" --body "$(cat <<'EOF'
[plan template content]
EOF
)"
```

**Markdown:** File to `plans/[feature]-refactor.md`. Standard pipeline applies.
