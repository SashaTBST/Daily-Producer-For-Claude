---
name: improve-codebase-architecture
description: Audits a codebase or vault for AI-navigability and deep module design — surfaces shallow modules, then generates 3–4 radically different interface redesigns in parallel before recommending one. Use when a project has grown complex, AI is losing context, or files have become shallow with large interfaces.
argument-hint: "[codebase path, project name, or specific concern]"
---

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

If a path, project, or concern is provided: $ARGUMENTS

## Purpose

A deep module (Ousterhout, A Philosophy of Software Design) has a small interface hiding a large implementation. Deep modules are more testable, more AI-navigable — test at the boundary instead of inside. This skill surfaces shallow modules and designs better ones.

## Workflow

**Step 1 — Explore organically**
Use subagent_type=Explore to navigate naturally. Note friction points:
- Understanding one concept requires bouncing between many small files
- Modules so shallow the interface rivals the implementation in complexity
- Pure functions extracted for testability, but real bugs hide in how they're called
- Tightly-coupled modules creating integration risk at the seams
- Untested or hard-to-test areas

The friction you encounter IS the signal.

**Step 2 — Present candidates**
Numbered list of deepening opportunities. For each:
- **Cluster:** which modules/concepts
- **Why coupled:** shared types, call patterns, co-ownership of a concept
- **Test impact:** what existing tests would be replaced by boundary tests

Do NOT propose interfaces yet. Ask: "Which would you like to explore?"

**Step 3 — User picks a candidate**

**Step 4 — Frame the problem space**
Write user-facing explanation of the chosen candidate:
- Constraints any new interface must satisfy
- Dependencies it would need to rely on
- Rough illustrative sketch to make constraints concrete (not a proposal)

Present to user, then immediately proceed to Step 5 in parallel.

**Step 5 — Design multiple interfaces in parallel**
Spawn 3–4 sub-agents simultaneously via Agent tool. Each produces a radically different interface. Give each agent file paths, coupling details, what's being hidden, and a distinct constraint:

- **Agent 1:** "Minimize the interface — 1–3 entry points max"
- **Agent 2:** "Maximize flexibility — support many use cases and extension"
- **Agent 3:** "Optimize for the most common caller — make the default case trivial"
- **Agent 4** (if applicable): "Ports & adapters pattern for cross-boundary dependencies"

Each sub-agent outputs: interface signature → usage example → what complexity it hides → trade-offs.

Present designs sequentially, then compare in prose. Give a strong recommendation. If elements combine well, propose the hybrid.

**Step 6 — User picks (or accepts recommendation)**

**Step 7 — Document the RFC**
Ask: "Is this a GitHub-enabled project?"
- **Yes:** create RFC as GitHub issue via `gh issue create`, share URL
- **No (default):** save as `plans/[feature]-refactor.md`

Do not create the issue or file without confirming which output the operator wants.

## Anti-patterns

- Running Step 5 sequentially — defeats the parallel design comparison
- Proposing a single interface in Step 2 instead of a numbered candidate list
- Auto-creating a GitHub issue without asking if the project is GitHub-enabled
- Big-bang rewrites — one candidate at a time
- Skipping Step 4 (framing) before spawning sub-agents — agents need the constraint context

## QA

Done when: operator has selected a design, RFC is documented (GitHub issue or markdown plan), and no further candidates are queued. Every response ends with NEXT MOVE.
