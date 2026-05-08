---
name: gsap
description: GSAP (GreenSock Animation Platform) animation builder for vanilla JS, React, and framework contexts. Covers tweens, timelines, ScrollTrigger, Flip, Draggable, SplitText, and all plugins. All plugins are free â€” no Club membership required. Use when building complex animations, scroll-linked effects, or sequenced motion.
argument-hint: "[mode: write|scroll|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/gsap = complex animations, timelines, scroll effects, SVG motion, runtime-controlled sequences.
Simple hover/transition â†’ use CSS. Basic one-shot animation â†’ consider CSS or Web Animations API first.
State context if known: vanilla JS, React component, or framework (Astro, Next.js, Nuxt).

## Plugins
All GSAP plugins are free â€” no Club membership or auth token required (Webflow partnership).
Install: `npm install gsap`. All plugins (ScrollTrigger, Flip, Draggable, MorphSVG, SplitText, ScrollSmoother, Observer) included.
NON-DEV: mention this upfront â€” operators may expect a paywall that no longer exists.

## Modes

**WRITE** â€” Tweens, timelines, sequences, easing, properties.
Import: `import gsap from 'gsap'`. Vite: named plugin imports â€” `import { ScrollTrigger } from 'gsap/ScrollTrigger'`.
Register plugins once at app entry file â€” never inside components: `gsap.registerPlugin(ScrollTrigger)`.
Performance: animate `x`/`y` not `left`/`top` (layout thrash). `force3D: true` for GPU â€” note blurriness risk on non-retina displays. Clear will-change after animation: `onComplete: () => gsap.set(el, { willChange: 'auto' })`.
React: always `useGSAP()` from `@gsap/react`. `contextSafe()` for any event handlers added after mount. `scope` prop to isolate selectors to component.
Vanilla JS SPA: wrap animations in `gsap.context()`, call `ctx.revert()` on page leave.
Never animate user-supplied data into DOM `innerHTML` â€” XSS vector. Use `textContent` or sanitize first.
NON-DEV: plain-English comment on any easing function or timeline method.
End with: propose `/scroll` for ScrollTrigger integration, `/typescript` for types.

**SCROLL** â€” ScrollTrigger: scroll-linked animation, pinning, scrubbing, snap.
Always call `ScrollTrigger.refresh()` after all triggers created â€” pinned elements shift downstream positions.
React: `requestAnimationFrame(() => ScrollTrigger.refresh())` â€” wait one frame for layout to settle.
Cleanup: `ctx.revert()` inside `useGSAP` return â€” never `ScrollTrigger.getAll().forEach(t => t.kill())` (kills other components' triggers).
Pinning: `pin: true` + `anticipatePin: 1`. `pinSpacing: false` when stacking full-screen sections.
ScrollSmoother requires ScrollTrigger registered first â€” both from the gsap package.
NON-DEV: explain start/end trigger strings ("top center", "bottom 80%") before writing code.
End with: propose `/review` for performance audit.

**REVIEW** â€” Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) layout-triggering properties (left/top/width/height), (2) GPU overuse and will-change abuse, (3) plugin and context cleanup on unmount, (4) security â€” innerHTML risk, CSS custom property injection, CSP compatibility.
Full per-item checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis for broken animations.
Open with these 4 intake questions before diagnosing:
  1. What should happen that isn't? (describe expected vs actual)
  2. Does it work in vanilla JS but break in React?
  3. Any console errors or warnings? (paste them)
  4. Which plugin is involved â€” or is this a tween/timeline issue?
Common causes and fixes in REFERENCE.md. Output: diagnosis + targeted fix. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and SCROLL response ends with this line â€” never skip it:
`Security pass: âœ“ no user data in innerHTML âœ“ CSS custom properties sanitized âœ“ contextSafe() used for post-mount handlers âœ“ ctx.revert() / onComplete cleanup present`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` â€” vanilla JS context for GSAP
- `/react` â€” component context; useGSAP hook applies
- `/css` â€” flag CSS/GSAP conflicts when animating same property
- `/html` â€” SVG and DOM elements being animated
- `/typescript` â€” gsap package includes full type definitions

## Pipeline Connections
WRITE complete â†’ propose `/scroll` or `/typescript`
SCROLL complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never animate `left`/`top` â€” use `x`/`y` (layout thrash, ~10Ã— slower)
âœ— Never use `innerHTML` with user-supplied data for animated content â€” XSS
âœ— Never use `useEffect` for GSAP in React â€” always `useGSAP()`
âœ— Never register plugins inside a component â€” once at app entry file only
âœ— Never use `ScrollTrigger.getAll().forEach(t => t.kill())` in React â€” kills other components' triggers
âœ— Never skip `ctx.revert()` cleanup in React or SPA context â€” memory leaks
âœ— Never mix GSAP and CSS transitions on the same property â€” unpredictable conflicts
âœ— Never leave `will-change` set after animation ends â€” clear in `onComplete`
âœ— Never skip the security gate line on WRITE/SCROLL output
âœ— Never default-import GSAP plugins in Vite â€” use named imports from plugin subpath


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.