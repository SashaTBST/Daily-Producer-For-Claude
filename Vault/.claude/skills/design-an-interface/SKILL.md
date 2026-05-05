---
name: design-an-interface
description: Generates multiple radically different interface designs for a module using parallel sub-agents, then compares trade-offs before building. Use when designing an API, exploring interface options, or when the user says "design it twice".
---

Based on "Design It Twice" (A Philosophy of Software Design): your first idea is unlikely to be the best. Generate multiple radically different designs. Compare. Then choose.

Do NOT implement during this skill — interface shape only.

## Anti-patterns

- Presenting similar variations as "radically different" — each design must have a genuinely different interface philosophy.
- Implementing anything during the design phase — this skill produces an interface shape, not code.
- Skipping the comparison — designs without trade-off analysis are just options, not decisions.
- Using tables for comparison — prose reveals nuance that tables can't.

## Workflow

**1. Gather requirements**
- What problem does this module solve?
- Who are the callers? (other modules, external users, tests)
- What are the key operations?
- Any constraints? (performance, compatibility, existing patterns)
- What should be hidden vs exposed?

**2. Generate designs (parallel sub-agents)**
Spawn 3+ sub-agents simultaneously. Each must produce a radically different approach:
- Agent 1: "Minimise method count — aim for 1–3 methods max"
- Agent 2: "Maximise flexibility — support many use cases"
- Agent 3: "Optimise for the most common case"
- Agent 4 (optional): "Take inspiration from [specific paradigm]"

Each agent outputs: interface signature, usage example, what it hides internally, trade-offs.

**3. Present and compare**
Show each design: interface signature, usage examples, what complexity is hidden.
Compare on: interface simplicity, general-purpose vs specialised, implementation efficiency, depth.
Prose only — no tables.

**4. Synthesise**
Ask which design fits the primary use case. Identify elements from other designs worth incorporating.

## Evaluation criteria (A Philosophy of Software Design)

- **Depth** — small interface hiding significant complexity = good. Large interface / thin implementation = avoid.
- **Simplicity** — fewer methods, simpler params = easier to use correctly.
- **Ease of correct use** vs ease of misuse.

## QA
Before closing this skill session:
- [ ] At least 3 radically different designs generated (not variations)
- [ ] Trade-offs compared in prose, not summarised vaguely
- [ ] No implementation written — interface shapes only
- [ ] Operator made an explicit choice before session closed
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.
