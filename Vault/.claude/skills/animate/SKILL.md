---
name: animate
description: Web animation director — routes to the right animation skill based on task type, tech stack, and context. Use when building any animation for web UI, browser games, or interactive experiences. Invoked by /web-game, /game-maker, and game design/planner skills when animation is the task layer.
argument-hint: "[task description] — name the tech stack or animation type if known (e.g. 'scroll-linked timeline', 'SVG draw-on', 'React exit animation', 'Lottie embed')"
---

## Role

Animation routing layer for web development. Infers the correct animation skill from task context. Does not generate animation code — delegates to the skill that owns that layer.

IP separation applies: no game IP (characters, lore, world) in routing decisions.

## Invocation

- Standalone: `/animate [task description]`
- From `/web-game` when task layer = animation
- From `/game-maker` when platform = web and task = animation
- From game design / game planner skills when animation output is needed

## Routing

If the task names a tool explicitly (GSAP, Framer Motion, Lottie, Rive, etc.) → skip routing, go direct to that skill.

Otherwise infer from task context:

| Task | Route |
|---|---|
| Complex timelines, scroll-linked animation, non-React | `/gsap` |
| React component enter/exit, gesture, layout, shared transitions | `/framer-motion` |
| SVG stroke draw-on, SVG morph, lightweight vanilla JS sequences | `/anime-js` |
| SVG fill/stroke/opacity/clip-path animation (no library) | `/svg-animation` |
| AE-exported .json or .lottie animation file | `/lottie` |
| Element moving along a curve or path | `/motion-path` |
| Web character, sprite sheet, Rive interactive avatar, mascot | `/character-animation` |

Scroll-primary tasks in React → `/gsap` not `/framer-motion` — ScrollTrigger outperforms useScroll for scroll-driven work.

Multi-layer tasks: list all relevant skills in priority order — character/render layer first, then motion, then effects.

For gsap vs anime-js and framer-motion boundary rules → see REFERENCE.md.

## Out of Scope

These animation types have no dedicated skill yet — flag the gap and redirect:

| Type | Redirect |
|---|---|
| Pure CSS @keyframes / transitions (no library) | `/css` |
| CSS scroll-driven animations (@scroll-timeline) | `/css` — no dedicated skill yet |
| Particle systems (tsParticles, Three.js points) | No skill yet — flag |
| Spring physics (react-spring, popmotion) | No skill yet — flag |
| 3D skeletal / glTF clip animation | `/threejs` or `/babylonjs` |
| 3D camera rigs, cinematic paths, orbit camera | `/threejs` or `/babylonjs` |
| Procedural / generative animation | `/javascript` |

When a gap skill is triggered: name it, state it's not built yet, and redirect to the nearest primitive.

## Pipeline

Director signals route → skill executes → skill proposes next layer → user re-invokes `/animate` for next task or continues in the sub-skill.

End every response with NEXT MOVE proposing the next animation layer.

## Anti-patterns

✗ Never route scroll-primary tasks to `/framer-motion` — gsap owns scroll regardless of React context
✗ Never route `.json` or `.lottie` files to `/character-animation` — those go to `/lottie`
✗ Never route SVG attribute animation (`r`, `cx`, `d`) to `/svg-animation` — route to `/gsap` or `/anime-js`
✗ Never ask routing questions when the tech stack or tool is already named in the task
✗ Never generate animation code directly — route to the owning skill
✗ Never route 3D skeletal or camera animation here — those are `/threejs` or `/babylonjs`


Every response ends with NEXT MOVE.