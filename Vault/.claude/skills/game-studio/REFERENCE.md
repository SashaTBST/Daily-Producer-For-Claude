# /game-studio — REFERENCE

## Engine MCP Table

| Engine / Tool | MCP | Install | Capabilities |
|---|---|---|---|
| Unreal Engine 5 | Unreal_mcp (ChiR24) | github.com/ChiR24/Unreal_mcp | 305+ tools: actor management, Blueprint create/edit, viewport, materials |
| Unity | unity-mcp (IvanMurzak) | github.com/IvanMurzak/Unity-MCP | Asset/scene/material, scripts, prefabs, full test loop |
| Godot | Godot MCP | lobehub.com/mcp/godot | Scene creation, code gen/fix, project analysis, performance |
| GameMaker | Native Claude Code | Built-in (GMRT 2026-04-30) | First-party engine integration — direct skill invocation |
| Blender | Anthropic native | claude.ai integrations | Scene ops, Python API, batch mesh/material/render |
| Cinema 4D | cinema4d-mcp | github.com/ttiimmaacc/cinema4d-mcp | Modeling, MoGraph, rendering via prompts |
| Maya / Houdini / 3ds Max / Nuke | dcc-mcp (universal) | github.com/loonghao/dcc-mcp | Model, rig, animate, render — unified adapter |

**Pre-flight check:** Before Phase 1 fires, ping each MCP the project requires. Report:
```
MCP STATUS:
  Unreal_mcp: [ACTIVE / INACTIVE]
  unity-mcp:  [ACTIVE / INACTIVE]
  Blender:    [ACTIVE / INACTIVE]
  ...
Inactive MCPs: roles depending on these will be skipped or run spec-only.
```

---

## Platform Workflow Modules

### Browser / Web
**Engines:** Phaser, Babylon.js, Three.js, PlayCanvas, PixiJS
**No MCP needed** — Claude generates JS/GLSL/WASM directly
**Pipeline:**
1. Generate game code (JS/TS + WebGL/WebGPU shaders)
2. Bundle: Vite or webpack
3. Test: local browser + Playwright for automated checks
4. Deploy: Netlify / Vercel / itch.io
**Notes:** WebGPU arriving (PlayCanvas leading). WASM for compute-heavy logic. Fastest path — no cert, no store gate. `/web-game` director handles routing within this platform.

### Mobile (iOS / Android)
**Engines:** Unity 6.3+ LTS, Godot 4.6+, Unreal
**MCPs:** unity-mcp, Godot MCP
**Pipeline:**
1. Build via engine MCP
2. Sign: Xcode (iOS) / Android Studio (Android)
3. CI: fastlane for automated build + upload
4. TestFlight (iOS) / Google Play Internal Track (Android)
5. Store review → release
**Notes:** Apple review ~1-3 days. Google ~hours. Godot 4.6 has native StoreKit 2 + Google Play Billing (Foundation-maintained). IAP and leaderboards no longer require community plugins.

### PC (Steam / Epic Games Store)
**Engines:** UE5, Unity, Godot, GameMaker
**MCPs:** Unreal_mcp, unity-mcp, Godot MCP, GameMaker native
**Pipeline:**
1. Build via engine MCP
2. CI: GitHub Actions (build, test, package)
3. Steam: depot upload → branch testing (default/beta/public) → release
4. EGS: BuildPatch tool → submission
**Notes:** Most permissive — no cert gate, just store review. Steam Next Fest = key wishlist milestone (Oct + Jun windows).

### Console (PlayStation / Xbox / Nintendo Switch)
**Engines:** UE5, Unity, Godot (via W4Consoles — commercial)
**MCPs:** engine MCPs for build only
**Pipeline:**
1. Build via engine MCP → console target
2. Internal QA + compliance review
3. **HARD STOP — manual gate** → submit to platform cert team
4. TRC (PlayStation) / XR (Xbox) / Lotcheck (Nintendo) — 6-12 week cycle
5. Patch → resubmit if rejected
**CRITICAL:** Platform SDKs are NDA-restricted. No AI tooling at the cert layer. AI pipeline ends at build complete. W4Consoles required for Godot → Switch/PS5/XSX (commercial product, not AI-native). Registered developer status required for all console platforms.

---

## Role Load Table

| Phase | Role | File | Type | Pass condition |
|---|---|---|---|---|
| 1 | Narrative | roles/narrative.md | Spec | Handoff package written |
| 1 | Production | roles/production.md | Spec | Handoff package written |
| 1 | UI/UX | roles/ui-ux.md | Spec | Handoff package written |
| 2 | Concept Art | roles/concept-art.md | Design | Assets at path + MCP import confirmed |
| 2 | Environment | roles/environment.md | Design | Assets at path + MCP import confirmed |
| 2 | Audio | roles/audio.md | Design | Files at path + non-silent waveform |
| 3 | Gameplay | roles/gameplay.md | Build | Compiles + behaviour test passes |
| 3 | Weapon Systems | roles/weapon-systems.md | Build | Compiles + behaviour test passes |
| 3 | Interaction Systems | roles/interaction-systems.md | Build | Compiles + behaviour test passes |
| 3 | Skybox & VFX | roles/skybox-vfx.md | Build | MCP import confirmed + no shader errors |
| 3 | Animation | roles/animation.md | Build | MCP import confirmed + clips play |
| 3 | Blueprints (UE5) | roles/blueprints-ue5.md | Build | UE5 only — compiles + no BP errors |
| 3 | Multiplayer | roles/multiplayer.md | Build | If scoped — session connects |
| 4 | QA | roles/qa.md | Verify | Zero open critical/high issues |

**Role activation signal:**
```
ROLE ACTIVE: [name] — [one-line scope]. Reading handoff from [prior role].
```
**Role completion signal:**
```
ROLE COMPLETE: [name] — handoff written to [project]/handoffs/[role]-handoff.md
```

---

## Handoff Package Contract

Every role produces before exiting:

```
[project]/handoffs/[role-name]-handoff.md

---
role: [name]
status: PASS | FAIL
iteration: [1/2/3]
---

## Output summary
[what was produced]

## Asset paths
[file paths written or imported]

## For next role
[what the next role needs to know — constraints, decisions, open items]

## Pass condition met
[how this role confirmed pass]
```

Next role reads this file as first action. No role runs without a prior handoff (except Phase 1 Spec roles which read the game brief directly).

---

## Loop Protocol Detail

```
Iteration 1:
  RED   — read handoff, define acceptance criteria for this role
  GREEN — generate output, import via MCP
  Check — does output meet criteria?
  PASS  → write handoff, signal ROLE COMPLETE, advance pipeline
  FAIL  → REFACTOR

Iteration 2:
  REFACTOR — analyse failure, adjust approach, re-generate
  Check — pass?
  PASS  → write handoff, advance
  FAIL  → Iteration 3

Iteration 3:
  Final attempt
  PASS  → advance
  FAIL  → ESCALATE:
    "ROLE BLOCKED: [role] — 3 iterations failed.
     Failure: [what broke]
     Options: [A] operator input + retry / [B] skip role / [C] abort pipeline"
    Wait for operator before proceeding.
```

---

## Output Routing

After QA passes:

```
Step 1 — In-tool write (via MCP)
  Engine MCP confirms final assets imported to project

Step 2 — Local path write
  Output directory: [project-root]/builds/[platform]/[YYYY-MM-DD]/
  Contents: build artifacts, handoff packages, QA report

Step 3 — Render pipeline path (if applicable)
  Assets for rendering: [project-root]/render/[YYYY-MM-DD]/
  Triggered for: concept art, environment, VFX, animation outputs

Step 4 — GitHub push
  Commit message format: feat([role]): [what was produced] — studio loop pass [N]
  Branch: feature/game-studio-[YYYY-MM-DD]
  PR: auto-created with handoff summary as PR body
```

---

## Prior Art

- `Donchitos/Claude-Code-Game-Studios` — 49 agents, 72 skills, 3-tier hierarchy (Directors/Leads/Specialists). Coordination scaffold only — no MCP, no loop, no platform routing. Reference for agent hierarchy patterns.
- `HermeticOrmus/claude-code-game-development` — lighter pattern library
- GameMaker native Claude Code (GMRT, 2026-04-30) — first-party engine integration precedent
