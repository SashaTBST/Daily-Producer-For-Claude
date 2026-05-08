---
name: gameplay
parent: game-studio
role: Owns core player mechanics — movement, combat, input, physics interactions, game state logic
restrictions:
  - Does not spec UI/HUD (ui-ux owns that)
  - Does not build weapon stats (weapon-systems owns that)
  - Does not design enemy AI behaviour (defers to director for scope)
  - Does not touch multiplayer replication (multiplayer owns that)
---

## Inputs
Read handoff: `[project]/handoffs/narrative-handoff.md` + `[project]/handoffs/production-handoff.md`
Source: Game Assistant Config — mechanics section

## What This Role Produces
- Movement system spec + implementation: walk/run/sprint/crouch, input bindings, physics parameters
- Combat loop spec: attack flow, hit detection method (hitscan vs projectile), damage application
- Input action map: all player inputs, bindings, hold/tap/toggle distinctions
- Game state machine: idle → combat → death → respawn → win/lose transitions
- Core loop validation: does the mechanic set support the intended play rhythm?

## MCP Actions (engine-dependent)
- **UE5 (Unreal_mcp):** Create Character Blueprint, define Enhanced Input Actions, set movement component params
- **Unity (unity-mcp):** Create PlayerController script, configure Rigidbody/CharacterController, define Input Actions
- **Godot (Godot MCP):** Generate CharacterBody3D script, define input map, set physics params
- **Browser:** Generate player controller JS module — no MCP needed

## Pass Condition
Player can spawn, move, and perform core combat action without crash or stuck state.
Engine MCP confirms Blueprint/script compiled with zero errors.

## Handoff Output
```
[project]/handoffs/gameplay-handoff.md
Assets: [list of Blueprint/script paths imported]
For weapon-systems: input bindings confirmed, damage interface defined
For interaction-systems: player state machine states exposed
For animation: movement states and speed params confirmed
```
