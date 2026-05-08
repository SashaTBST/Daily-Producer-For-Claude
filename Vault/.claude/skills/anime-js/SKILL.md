---
name: anime-js
description: Anime.js v4 animation builder for vanilla JS, React, and framework contexts. Covers animate(), createTimeline(), SVG morphing, motion paths, and stroke drawables. Named imports only â€” no default export. Use when building sequenced multi-step animations, SVG effects, or timeline-controlled motion.
argument-hint: "[mode: write|timeline|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/anime-js = sequenced animations, multi-target timelines, SVG stroke/path/morph effects.
Simple hover/transition â†’ use CSS. Scroll-linked animation â†’ use /gsap (ScrollTrigger). Complex sequencing with React â†’ /gsap (useGSAP hook handles Strict Mode; anime-js does not).
State context if known: vanilla JS, React, or framework (Astro, Next.js).

## Install
```
npm install animejs
```
Named imports only â€” `import { animate, createTimeline, createScope } from 'animejs'`. No default export in v4.

## Modes

**WRITE** â€” Single animations: animate(), properties, easing, composition.
`animate(targets, { duration, ease, delay, loop, autoplay, composition })` â€” returns animation instance.
Targets: CSS selector string, DOM element, array of elements, or plain JS object.
Animatable: CSS transforms (`translateX`, `rotate`, `scale`), CSS properties, SVG attributes, CSS variables, JS object properties.
Use transforms â€” never `left`/`top`/`width` (layout thrash, ~10Ã— slower).
Composition default is `'replace'` â€” new animation cancels prior one on same target. Use `composition: 'blend'` to layer.
Cleanup: `.revert()` (cancels + reverts inline styles + cleans memory). Preferred over `.cancel()`.
React: use `createScope()` + `useEffect`. Add React 18 Strict Mode warning â€” createScope() is the official pattern but does not have a dedicated hook; double-invoke in dev mode may cause duplicate animations. Recommend gating with a `ref` flag or switching to /gsap for React-heavy projects.
NON-DEV: explain what each property does before writing code.
End with: propose `/timeline` for sequencing, `/review` for performance audit.

**TIMELINE** â€” Sequenced multi-step animation: createTimeline(), offset syntax, sync, callbacks.
`createTimeline({ defaults: { duration: 750, ease: 'outExpo' }, playbackEase: 'inOutSine' })`.
`.add(targets, props, offset)` â€” offset: absolute ms `500`, relative `'+=300'` (after previous ends), `'<'` (same start as previous), `'<+=200'` (200ms after previous starts).
`.sync(otherTimeline)` â€” embed a nested timeline as a child.
`.call(callback, offset)` â€” fire a function at a point in the timeline.
`.label(name, offset)` â€” named marker for scrubbing or `.seek(labelName)`.
`.play()` / `.pause()` / `.revert()` on the timeline instance.
NON-DEV: diagram the sequence in plain English before writing code â€” "step 1 starts at 0ms, step 2 starts 300ms after step 1 endsâ€¦"
End with: propose `/review` for performance audit.

**REVIEW** â€” Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) layout-triggering properties, (2) composition conflicts, (3) cleanup on unmount, (4) security â€” CSS custom property injection, SVG attribute injection.
Full per-item checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis for broken animations.
Open with these 4 intake questions before diagnosing:
  1. What should happen that isn't? (expected vs actual)
  2. Is this vanilla JS or React? React 18 Strict Mode?
  3. Is it a single animate() or a createTimeline() issue?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and TIMELINE response ends with this line â€” never skip it:
`Security pass: âœ“ transforms used not layout props âœ“ CSS custom properties are static not user-supplied âœ“ SVG attributes are static not user-supplied âœ“ .revert() cleanup present`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` â€” vanilla JS context
- `/react` â€” component context; note createScope() + useEffect pattern and Strict Mode risk
- `/css` â€” flag CSS/anime-js conflicts when animating the same property
- `/html` â€” SVG and DOM elements being animated
- `/typescript` â€” animejs package includes type definitions
- `/gsap` â€” recommend for scroll-linked animation or React-heavy projects

## Pipeline Connections
WRITE complete â†’ propose `/timeline` or `/review`
TIMELINE complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never use default import â€” `import anime from 'animejs'` fails in v4
âœ— Never use v3 easing format â€” `'easeOutExpo'` fails silently; use `'outExpo'`
âœ— Never use v3 `anime({targets: ...})` syntax â€” v4 is `animate(targets, {...})`
âœ— Never animate `left`/`top`/`width` â€” use `translateX`/`translateY`/`scaleX`
âœ— Never rely on composition default â€” state `composition: 'blend'` or `'replace'` explicitly when layering
âœ— Never skip `.revert()` cleanup in React or SPA â€” memory leaks and ghost animations
âœ— Never use `utils.remove(targets)` inside a component â€” removes from ALL active animations globally
âœ— Never animate user-supplied data into SVG attributes or CSS custom properties â€” injection risk
âœ— Never skip the security gate line on WRITE/TIMELINE output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.