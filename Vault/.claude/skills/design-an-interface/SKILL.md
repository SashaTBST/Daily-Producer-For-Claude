---
name: design-an-interface
description: Generate multiple radically different interface designs for a module using parallel sub-agents. Use when user wants to design an API, explore interface options, compare module shapes, or mentions "design it twice".
---

# Design an Interface

Based on "Design It Twice" from A Philosophy of Software Design: your first idea is unlikely to be the best. Generate multiple radically different designs, then compare.

## Workflow

### 1. Gather requirements

- [ ] What problem does this module solve?
- [ ] Who are the callers? (other modules, external users, tests)
- [ ] What are the key operations?
- [ ] Any constraints? (performance, compatibility, existing patterns)
- [ ] What should be hidden vs exposed?

### 2. Generate designs (parallel sub-agents)

Spawn 3+ sub-agents simultaneously. Each must produce a **radically different** approach. Assign a different constraint to each:
- Agent 1: "Minimise method count — aim for 1–3 methods max"
- Agent 2: "Maximise flexibility — support many use cases"
- Agent 3: "Optimise for the most common case"
- Agent 4 (optional): "Take inspiration from [specific paradigm]"

Each agent outputs: interface signature, usage example, what it hides internally, trade-offs.

### 3. Present designs

Show each design sequentially. Include interface signature, usage examples, what complexity is hidden.

### 4. Compare

Compare on: interface simplicity, general-purpose vs specialised, implementation efficiency, depth (small interface / large implementation = good). Discuss trade-offs in prose, not tables.

### 5. Synthesise

Ask which design fits the primary use case. Identify elements from other designs worth incorporating. The best design often combines insights from multiple options.

## Evaluation criteria (from A Philosophy of Software Design)

- **Depth** — small interface hiding significant complexity = good. Large interface with thin implementation = avoid.
- **Simplicity** — fewer methods, simpler params = easier to use correctly
- **Ease of correct use** vs **ease of misuse**

Do NOT implement during this skill — interface shape only.
