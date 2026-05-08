Audit and improve the architecture of a codebase or vault for AI-navigability and deep module design. Use when a project has grown complex, AI is losing context, or files have become shallow with large interfaces.

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

If a codebase path, project, or specific concern is provided: $ARGUMENTS

## Purpose

A deep module (Ousterhout, A Philosophy of Software Design) has a small interface hiding a large implementation. Deep modules are more testable, more AI-navigable — test at the boundary instead of inside. This skill surfaces shallow modules and designs better ones.

---

## Process

**Step 1 — Explore organically**
Use subagent_type=Explore to navigate the codebase naturally. Do NOT follow rigid heuristics — explore organically and note where friction occurs:
- Where does understanding one concept require bouncing between many small files?
- Where are modules so shallow the interface is nearly as complex as the implementation?
- Where have pure functions been extracted for testability, but real bugs hide in how they're called?
- Where do tightly-coupled modules create integration risk at the seams?
- Which parts are untested or hard to test?

The friction you encounter IS the signal.

**Step 2 — Present candidates**
Present a numbered list of deepening opportunities. For each:
- **Cluster:** which modules/concepts are involved
- **Why they're coupled:** shared types, call patterns, co-ownership of a concept
- **Test impact:** what existing tests would be replaced by boundary tests

Do NOT propose interfaces yet. Ask: "Which would you like to explore?"

**Step 3 — User picks a candidate**

**Step 4 — Frame the problem space**
Write a user-facing explanation of the chosen candidate:
- The constraints any new interface would need to satisfy
- The dependencies it would need to rely on
- A rough illustrative code sketch to make the constraints concrete (not a proposal — just grounds the constraints)

Show this to the user, then immediately proceed to Step 5. The user reads while the sub-agents work in parallel.

**Step 5 — Design multiple interfaces in parallel**
Spawn 3–4 sub-agents simultaneously using the Agent tool. Each must produce a radically different interface. Give each a separate technical brief (file paths, coupling details, what's being hidden) and a different design constraint:

- **Agent 1:** "Minimize the interface — aim for 1–3 entry points max"
- **Agent 2:** "Maximize flexibility — support many use cases and extension"
- **Agent 3:** "Optimize for the most common caller — make the default case trivial"
- **Agent 4** (if applicable): "Design around the ports & adapters pattern for cross-boundary dependencies"

Each sub-agent must output:
1. Interface signature (types, methods, params)
2. Usage example showing how callers use it
3. What complexity it hides internally
4. Trade-offs

Present designs sequentially, then compare in prose. Give a strong recommendation — which design is strongest and why. If elements from different designs combine well, propose the hybrid. Be opinionated.

**Step 6 — User picks (or accepts recommendation)**

**Step 7 — Create GitHub issue**
Create a refactor RFC as a GitHub issue using `gh issue create`. Do NOT ask the user to review before creating — just create it and share the URL. For markdown-only projects: save as `plans/[feature]-refactor.md`.

---

## Rules

- One change at a time — no big-bang rewrites
- Test or verify after every change
- Deep modules over shallow — small interface, large implementation
- AI-navigability is the primary metric
- Flag dependencies before moving files

---

Every response ends with NEXT MOVE.
