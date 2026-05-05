# animate — Reference

## gsap vs anime-js Decision

Both handle vanilla JS timelines and SVG animation. Use this to decide:

| Signal | Route |
|---|---|
| React context | `/gsap` — useGSAP hook + Strict Mode safe |
| Scroll-linked animation | `/gsap` — ScrollTrigger |
| Complex control flow (seek, reverse, pause, scrub) | `/gsap` |
| SVG stroke draw-on (createDrawable) | `/anime-js` |
| SVG path morph (morphTo) | `/anime-js` |
| SVG motion path (createMotionPath) | `/motion-path` first — it routes internally |
| Lightweight vanilla JS multi-target sequence, no scroll | `/anime-js` |

**Rule:** When in doubt, gsap. anime-js wins only when the task is SVG-native (stroke, morph, draw) or a lightweight non-React sequence with no scroll requirement.

---

## gsap vs framer-motion Decision

Both work in React. Use this to decide:

| Signal | Route |
|---|---|
| Component lifecycle animation (enter/exit/unmount) | `/framer-motion` |
| Gesture-responsive animation (hover/tap/drag) | `/framer-motion` |
| Layout animation, size/position change animation | `/framer-motion` |
| Shared element transitions between routes | `/framer-motion` |
| Scroll-linked animation in React | `/gsap` |
| Timeline control (seek, reverse, sequence) in React | `/gsap` |
| Non-React context | `/gsap` |

**Rule:** framer-motion = component lifecycle. gsap = everything else including scroll and timelines inside React.

---

## Multi-Layer Tasks

When a task spans multiple animation layers, list all relevant skills in priority order:

1. Character/render layer (what is being animated)
2. Motion layer (how it moves — path, scroll, timeline)
3. Effects layer (particles, shaders — flag gap skill if none exists)

Example: "Sprite character walks along a curved path on scroll"
→ `/character-animation` (sprite setup) + `/motion-path` (curve) + `/gsap` (scroll link)

---

## Planned Skills Backlog

These animation types need dedicated skills. When a task matches: flag the gap, name the planned skill, redirect to nearest primitive.

| Planned Skill | Scope | Nearest Primitive |
|---|---|---|
| `/css-animation` | Pure CSS @keyframes, transitions, @scroll-timeline | `/css` |
| `/scroll-animation` | CSS scroll-driven + Intersection Observer | `/css` + `/gsap` |
| `/particle-system` | tsParticles, Three.js points, Vanta.js | No skill — docs redirect |
| `/spring-physics` | react-spring, popmotion, natural motion | No skill — docs redirect |
| `/3d-character-animation` | Three.js SkeletonHelper, glTF clips, morph targets | `/threejs` |
| `/camera-animation` | 3D orbit, first-person, cinematic camera paths | `/threejs` or `/babylonjs` |
| `/procedural-animation` | Noise-based, generative, algorithmic motion | `/javascript` |

**Update obligation:** When any planned skill is built, add it to the routing table in SKILL.md and remove it from this list.

---

## Security Inherited from Sub-Skills

Each sub-skill carries its own security gate. The director does not add a separate gate.

| Sub-skill | Key security gate |
|---|---|
| `/gsap` | No user data in innerHTML; ctx.revert() cleanup; contextSafe() for post-mount handlers |
| `/framer-motion` | No user input in CSS custom properties; tween on opacity/color (not spring) |
| `/anime-js` | Static CSS custom properties; static SVG attributes; .revert() cleanup |
| `/svg-animation` | No SVG attribute values from user input; d/href are static |
| `/lottie` | Source is static/trusted (not user-uploaded); prefers-reduced-motion checked |
| `/motion-path` | offset-path value is static; no user data in path() string |
| `/character-animation` | .riv file from trusted source; Rive inputs hardcoded; prefers-reduced-motion handled |

---

## /game-maker and /web-game Integration

`/animate` is loaded by:
- `/web-game` when the task layer is animation (not rendering, physics, or assets)
- `/game-maker` when platform = web and the task explicitly involves animation
- Game design / game planner skills when animation output is required

Signal pattern from /web-game or /game-maker:
```
Task layer: animation — loading /animate director.
Read .claude/skills/animate/SKILL.md
```

`/animate` does not load `/web-game` or `/game-maker` — routing is one-way (director → sub-skill, not circular).
