Create a detailed refactor plan with tiny commits via user interview, then file it as a GitHub issue or Markdown staging entry. Use when planning a refactor, creating a refactoring RFC, or breaking a refactor into safe incremental steps.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

**At start — ask:** "Is this project GitHub-enabled or Markdown-only?"
- **GitHub** → file plan as a GitHub issue
- **Markdown** → file plan to `plans/[feature]-refactor.md`

## Steps

1. **Interview** — Ask for a long, detailed description of the problem and any ideas for solutions.

2. **Explore** — Search the codebase to verify assertions and understand current state.

3. **Present alternatives** — Ask if they've considered other options. Present any alternatives you've identified.

4. **Deep interview on implementation** — Be extremely detailed and thorough. Resolve all open questions.

5. **Hammer scope** — Work out exactly what you plan to change and what you plan NOT to change. Write it down. Confirm with user.

6. **Check test coverage** — Find tests for the affected area. If insufficient, ask: "What's your plan for testing this?"

7. **Plan tiny commits** — Break implementation into the smallest possible commits. Each must leave the codebase in a working state. (Martin Fowler: "Make each refactoring step as small as possible.")

8. **File the plan** — See REFERENCE section for the full plan template.

**Do NOT include specific file paths or code snippets in the plan** — they go stale. Describe behaviour and interfaces instead.

Every response ends with NEXT MOVE.

---

## REFERENCE

### Plan Template

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

### Filing

**GitHub:**
```
gh issue create --title "refactor: [short description]" --body "$(cat <<'EOF'
[plan template content]
EOF
)"
```

**Markdown:** File to `plans/[feature]-refactor.md`. Standard pipeline applies.
