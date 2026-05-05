---
name: anime-js
description: Anime.js v4 animation builder for vanilla JS, React, and framework contexts. Covers animate(), createTimeline(), SVG morphing, motion paths, and stroke drawables. Named imports only — no default export. Use when building sequenced multi-step animations, SVG effects, or timeline-controlled motion.
argument-hint: "[mode: write|timeline|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/anime-js = sequenced animations, multi-target timelines, SVG stroke/path/morph effects.
Simple hover/transition → use CSS. Scroll-linked animation → use /gsap (ScrollTrigger). Complex sequencing with React → /gsap (useGSAP hook handles Strict Mode; anime-js does not).
State context if known: vanilla JS, React, or framework (Astro, Next.js).

## Install
```
npm install animejs
```
Named imports only — `import { animate, createTimeline, createScope } from 'animejs'`. No default export in v4.

## Modes

**WRITE** — Single animations: animate(), properties, easing, composition.
`animate(targets, { duration, ease, delay, loop, autoplay, composition })` — returns animation instance.
Targets: CSS selector string, DOM element, array of elements, or plain JS object.
Animatable: CSS transforms (`translateX`, `rotate`, `scale`), CSS properties, SVG attributes, CSS variables, JS object properties.
Use transforms — never `left`/`top`/`width` (layout thrash, ~10× slower).
Composition default is `'replace'` — new animation cancels prior one on same target. Use `composition: 'blend'` to layer.
Cleanup: `.revert()` (cancels + reverts inline styles + cleans memory). Preferred over `.cancel()`.
React: use `createScope()` + `useEffect`. Add React 18 Strict Mode warning — createScope() is the official pattern but does not have a dedicated hook; double-invoke in dev mode may cause duplicate animations. Recommend gating with a `ref` flag or switching to /gsap for React-heavy projects.
NON-DEV: explain what each property does before writing code.
End with: propose `/timeline` for sequencing, `/review` for performance audit.

**TIMELINE** — Sequenced multi-step animation: createTimeline(), offset syntax, sync, callbacks.
`createTimeline({ defaults: { duration: 750, ease: 'outExpo' }, playbackEase: 'inOutSine' })`.
`.add(targets, props, offset)` — offset: absolute ms `500`, relative `'+=300'` (after previous ends), `'<'` (same start as previous), `'<+=200'` (200ms after previous starts).
`.sync(otherTimeline)` — embed a nested timeline as a child.
`.call(callback, offset)` — fire a function at a point in the timeline.
`.label(name, offset)` — named marker for scrubbing or `.seek(labelName)`.
`.play()` / `.pause()` / `.revert()` on the timeline instance.
NON-DEV: diagram the sequence in plain English before writing code — "step 1 starts at 0ms, step 2 starts 300ms after step 1 ends…"
End with: propose `/review` for performance audit.

**REVIEW** — Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) layout-triggering properties, (2) composition conflicts, (3) cleanup on unmount, (4) security — CSS custom property injection, SVG attribute injection.
Full per-item checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis for broken animations.
Open with these 4 intake questions before diagnosing:
  1. What should happen that isn't? (expected vs actual)
  2. Is this vanilla JS or React? React 18 Strict Mode?
  3. Is it a single animate() or a createTimeline() issue?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and TIMELINE response ends with this line — never skip it:
`Security pass: ✓ transforms used not layout props ✓ CSS custom properties are static not user-supplied ✓ SVG attributes are static not user-supplied ✓ .revert() cleanup present`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — vanilla JS context
- `/react` — component context; note createScope() + useEffect pattern and Strict Mode risk
- `/css` — flag CSS/anime-js conflicts when animating the same property
- `/html` — SVG and DOM elements being animated
- `/typescript` — animejs package includes type definitions
- `/gsap` — recommend for scroll-linked animation or React-heavy projects

## Pipeline Connections
WRITE complete → propose `/timeline` or `/review`
TIMELINE complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never use default import — `import anime from 'animejs'` fails in v4
✗ Never use v3 easing format — `'easeOutExpo'` fails silently; use `'outExpo'`
✗ Never use v3 `anime({targets: ...})` syntax — v4 is `animate(targets, {...})`
✗ Never animate `left`/`top`/`width` — use `translateX`/`translateY`/`scaleX`
✗ Never rely on composition default — state `composition: 'blend'` or `'replace'` explicitly when layering
✗ Never skip `.revert()` cleanup in React or SPA — memory leaks and ghost animations
✗ Never use `utils.remove(targets)` inside a component — removes from ALL active animations globally
✗ Never animate user-supplied data into SVG attributes or CSS custom properties — injection risk
✗ Never skip the security gate line on WRITE/TIMELINE output


Every response ends with NEXT MOVE.