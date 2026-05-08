Generate multiple radically different interface designs for a module using parallel sub-agents, then compare trade-offs before building. Use when designing an API, exploring interface options, or when the user mentions "design it twice".

Read `_AI/daily-ai-config.md` — treat as loaded. All pipeline rules apply.

---

Based on "Design It Twice" from A Philosophy of Software Design: your first idea is unlikely to be the best. Generate multiple radically different designs, then compare.

## Workflow

### 1. Gather requirements

- What problem does this module solve?
- Who are the callers? (other modules, external users, tests)
- What are the key operations?
- Any constraints? (performance, compatibility, existing patterns)
- What should be hidden vs exposed?

### 2. Generate designs (parallel sub-agents)

Spawn 3+ sub-agents simultaneously. Each must produce a **radically different** approach:
- Agent 1: "Minimise method count — aim for 1–3 methods max"
- Agent 2: "Maximise flexibility — support many use cases"
- Agent 3: "Optimise for the most common case"
- Agent 4 (optional): "Take inspiration from [specific paradigm]"

Each agent outputs: interface signature, usage example, what it hides internally, trade-offs.

### 3. Present and compare

Show each design: interface signature, usage examples, what complexity is hidden.

Compare on: interface simplicity, general-purpose vs specialised, implementation efficiency, depth (small interface / large implementation = good). Use prose, not tables.

### 4. Synthesise

Ask which design fits the primary use case. Identify elements from other designs worth incorporating.

## Evaluation criteria (A Philosophy of Software Design)

- **Depth** — small interface hiding significant complexity = good. Large interface / thin implementation = avoid.
- **Simplicity** — fewer methods, simpler params = easier to use correctly
- **Ease of correct use** vs ease of misuse

Do NOT implement during this skill — interface shape only.

Every response ends with NEXT MOVE.
