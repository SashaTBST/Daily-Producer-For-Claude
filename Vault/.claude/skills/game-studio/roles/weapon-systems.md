---
name: weapon-systems
parent: game-studio
role: Owns weapon design and implementation — stats, feel, ammo economy, balance, hit registration
restrictions:
  - Does not own player movement (gameplay owns that)
  - Does not design enemy AI (defers to director)
  - Does not create weapon 3D models (environment/animation own assets)
  - Does not own multiplayer replication for weapons (multiplayer owns that)
---

## Inputs
Read handoff: `[project]/handoffs/gameplay-handoff.md`
Requires: damage interface and input bindings from gameplay role

## What This Role Produces
- Weapon spec sheet: damage, fire rate, range falloff, spread, recoil, reload time, magazine size
- Weapon feel brief: draw speed, ADS behaviour, recoil recovery, movement penalty
- Ammo economy: types, scarcity, pickup rates, carry limits
- Balance matrix: DPS/TTK comparison table across all weapons
- Hit registration spec: hitscan vs projectile per weapon, surface responses, penetration rules

## MCP Actions (engine-dependent)
- **UE5 (Unreal_mcp):** Create weapon Actor Blueprint, implement fire component, set collision profiles
- **Unity (unity-mcp):** Create weapon MonoBehaviour, configure raycast hit detection, set animator params
- **Godot (Godot MCP):** Generate weapon script, set RayCast3D parameters
- **Browser:** Generate weapon module JS — no MCP needed

## Pass Condition
Primary weapon fires, applies damage to a test target, and reloads without crash.
MCP confirms compile with zero errors. Ammo economy spec written.

## Handoff Output
```
[project]/handoffs/weapon-systems-handoff.md
Assets: [weapon Blueprint/script paths]
For animation: weapon state names confirmed (idle/aim/fire/reload)
For skybox-vfx: muzzle flash and impact VFX trigger points defined
For audio: fire/reload/dry-fire sound cue trigger events listed
For multiplayer: replicated weapon variables identified
```
