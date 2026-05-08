---
name: animate
description: Web animation director â€” routes to the right animation skill based on task type, tech stack, and context. Use when building any animation for web UI, browser games, or interactive experiences. Invoked by /web-game, /game-maker, and game design/planner skills when animation is the task layer.
argument-hint: "[task description] â€” name the tech stack or animation type if known (e.g. 'scroll-linked timeline', 'SVG draw-on', 'React exit animation', 'Lottie embed')"
---

## Role

Animation routing layer for web development. Infers the correct animation skill from task context. Does not generate animation code â€” delegates to the skill that owns that layer.

IP separation applies: no game IP (characters, lore, world) in routing decisions.

## Invocation

- Standalone: `/animate [task description]`
- From `/web-game` when task layer = animation
- From `/game-maker` when platform = web and task = animation
- From game design / game planner skills when animation output is needed

## Routing

If the task names a tool explicitly (GSAP, Framer Motion, Lottie, Rive, etc.) â†’ skip routing, go direct to that skill.

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

Scroll-primary tasks in React â†’ `/gsap` not `/framer-motion` â€” ScrollTrigger outperforms useScroll for scroll-driven work.

Multi-layer tasks: list all relevant skills in priority order â€” character/render layer first, then motion, then effects.

For gsap vs anime-js and framer-motion boundary rules â†’ see REFERENCE.md.

## Out of Scope

These animation types have no dedicated skill yet â€” flag the gap and redirect:

| Type | Redirect |
|---|---|
| Pure CSS @keyframes / transitions (no library) | `/css` |
| CSS scroll-driven animations (@scroll-timeline) | `/css` â€” no dedicated skill yet |
| Particle systems (tsParticles, Three.js points) | No skill yet â€” flag |
| Spring physics (react-spring, popmotion) | No skill yet â€” flag |
| 3D skeletal / glTF clip animation | `/threejs` or `/babylonjs` |
| 3D camera rigs, cinematic paths, orbit camera | `/threejs` or `/babylonjs` |
| Procedural / generative animation | `/javascript` |

When a gap skill is triggered: name it, state it's not built yet, and redirect to the nearest primitive.

## Pipeline

Director signals route â†’ skill executes â†’ skill proposes next layer â†’ user re-invokes `/animate` for next task or continues in the sub-skill.

End every response with NEXT MOVE proposing the next animation layer.

## Anti-patterns

âœ— Never route scroll-primary tasks to `/framer-motion` â€” gsap owns scroll regardless of React context
âœ— Never route `.json` or `.lottie` files to `/character-animation` â€” those go to `/lottie`
âœ— Never route SVG attribute animation (`r`, `cx`, `d`) to `/svg-animation` â€” route to `/gsap` or `/anime-js`
âœ— Never ask routing questions when the tech stack or tool is already named in the task
âœ— Never generate animation code directly â€” route to the owning skill
âœ— Never route 3D skeletal or camera animation here â€” those are `/threejs` or `/babylonjs`


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.