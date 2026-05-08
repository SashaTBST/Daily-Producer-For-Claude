---
name: interaction-systems
parent: game-studio
role: Owns player-world interactions — doors, switches, pickups, destructibles, triggers, interactables
restrictions:
  - Does not own core player movement (gameplay owns that)
  - Does not design puzzle logic beyond trigger/response (defers to director for puzzle scope)
  - Does not own UI prompts for interactions (ui-ux owns prompt display)
  - Does not build weapon pickup logic (weapon-systems owns that)
---

## Inputs
Read handoff: `[project]/handoffs/gameplay-handoff.md` + `[project]/handoffs/environment-handoff.md`
Requires: player state machine states from gameplay, level trigger zones from environment

## What This Role Produces
- Interaction inventory: all interactable object types with trigger method and feedback
- Door system: types (hinged/sliding/vault), lock states, health (destructible), pathfinding impact
- Pickup system: item types, radius, auto vs prompted, visual indicator rules
- Trigger volume map: zone events, conditions, one-shot vs repeating
- Destructible spec: health values, destruction stages, physics response, debris persistence

## MCP Actions (engine-dependent)
- **UE5 (Unreal_mcp):** Create IInteractable interface, door Actor Blueprint with state machine, trigger volumes
- **Unity (unity-mcp):** Create IInteractable interface, door script, OnTriggerEnter event wiring
- **Godot (Godot MCP):** Generate interactable script, Area3D trigger nodes, door AnimationPlayer
- **Browser:** Generate interaction manager JS module — no MCP needed

## Pass Condition
Player can open at least one door and pick up at least one item without crash.
MCP confirms compile and interaction component attached correctly.

## Handoff Output
```
[project]/handoffs/interaction-systems-handoff.md
Assets: [door/pickup/trigger Blueprint or script paths]
For audio: door open/close, pickup, and trigger audio event points listed
For skybox-vfx: destructible debris and trigger VFX spawn points defined
For qa: destructible physics edge cases flagged
```
