---
name: blueprints-ue5
parent: game-studio
role: UE5-only — owns Blueprint visual scripting, Actor component architecture, Event Graph logic
restrictions:
  - UE5 projects only — skip entirely for Unity/Godot/browser platforms
  - Does not own gameplay design decisions (gameplay role owns those)
  - Does not create materials or VFX (skybox-vfx owns that)
  - Does not write C++ (defers C++ to director if needed)
---

## Inputs
Read handoff: `[project]/handoffs/gameplay-handoff.md`
Requires: Unreal_mcp active (pre-flight confirmed)

## What This Role Produces
- Actor Component architecture: which logic lives in which component, interface definitions
- Blueprint Event Graph implementations for each gameplay system
- Blueprint communication patterns: Event Dispatchers, Interfaces, direct references — which to use per case
- Variable replication flags for multiplayer-scoped systems
- Blueprint compile validation — zero errors, zero warnings target

## MCP Actions (Unreal_mcp)
- Create Actor Blueprints per system
- Add components to Blueprint class hierarchy
- Define Custom Events and Event Dispatchers
- Set variable replication rules
- Compile and validate — confirm zero BP errors

## Pass Condition
All Blueprints compile clean. Core gameplay loop runs in PIE (Play In Editor) without crash.
Unreal_mcp confirms compile success on each Blueprint.

## Handoff Output
```
[project]/handoffs/blueprints-ue5-handoff.md
Assets: [list of Blueprint asset paths]
For weapon-systems: weapon component interface confirmed
For interaction-systems: interactable interface (IInteractable) created
For multiplayer: replicated variable list confirmed
```
