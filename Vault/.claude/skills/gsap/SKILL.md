---
name: gsap
description: GSAP (GreenSock Animation Platform) animation builder for vanilla JS, React, and framework contexts. Covers tweens, timelines, ScrollTrigger, Flip, Draggable, SplitText, and all plugins. All plugins are free — no Club membership required. Use when building complex animations, scroll-linked effects, or sequenced motion.
argument-hint: "[mode: write|scroll|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/gsap = complex animations, timelines, scroll effects, SVG motion, runtime-controlled sequences.
Simple hover/transition → use CSS. Basic one-shot animation → consider CSS or Web Animations API first.
State context if known: vanilla JS, React component, or framework (Astro, Next.js, Nuxt).

## Plugins
All GSAP plugins are free — no Club membership or auth token required (Webflow partnership).
Install: `npm install gsap`. All plugins (ScrollTrigger, Flip, Draggable, MorphSVG, SplitText, ScrollSmoother, Observer) included.
NON-DEV: mention this upfront — operators may expect a paywall that no longer exists.

## Modes

**WRITE** — Tweens, timelines, sequences, easing, properties.
Import: `import gsap from 'gsap'`. Vite: named plugin imports — `import { ScrollTrigger } from 'gsap/ScrollTrigger'`.
Register plugins once at app entry file — never inside components: `gsap.registerPlugin(ScrollTrigger)`.
Performance: animate `x`/`y` not `left`/`top` (layout thrash). `force3D: true` for GPU — note blurriness risk on non-retina displays. Clear will-change after animation: `onComplete: () => gsap.set(el, { willChange: 'auto' })`.
React: always `useGSAP()` from `@gsap/react`. `contextSafe()` for any event handlers added after mount. `scope` prop to isolate selectors to component.
Vanilla JS SPA: wrap animations in `gsap.context()`, call `ctx.revert()` on page leave.
Never animate user-supplied data into DOM `innerHTML` — XSS vector. Use `textContent` or sanitize first.
NON-DEV: plain-English comment on any easing function or timeline method.
End with: propose `/scroll` for ScrollTrigger integration, `/typescript` for types.

**SCROLL** — ScrollTrigger: scroll-linked animation, pinning, scrubbing, snap.
Always call `ScrollTrigger.refresh()` after all triggers created — pinned elements shift downstream positions.
React: `requestAnimationFrame(() => ScrollTrigger.refresh())` — wait one frame for layout to settle.
Cleanup: `ctx.revert()` inside `useGSAP` return — never `ScrollTrigger.getAll().forEach(t => t.kill())` (kills other components' triggers).
Pinning: `pin: true` + `anticipatePin: 1`. `pinSpacing: false` when stacking full-screen sections.
ScrollSmoother requires ScrollTrigger registered first — both from the gsap package.
NON-DEV: explain start/end trigger strings ("top center", "bottom 80%") before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) layout-triggering properties (left/top/width/height), (2) GPU overuse and will-change abuse, (3) plugin and context cleanup on unmount, (4) security — innerHTML risk, CSS custom property injection, CSP compatibility.
Full per-item checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis for broken animations.
Open with these 4 intake questions before diagnosing:
  1. What should happen that isn't? (describe expected vs actual)
  2. Does it work in vanilla JS but break in React?
  3. Any console errors or warnings? (paste them)
  4. Which plugin is involved — or is this a tween/timeline issue?
Common causes and fixes in REFERENCE.md. Output: diagnosis + targeted fix. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and SCROLL response ends with this line — never skip it:
`Security pass: ✓ no user data in innerHTML ✓ CSS custom properties sanitized ✓ contextSafe() used for post-mount handlers ✓ ctx.revert() / onComplete cleanup present`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/javascript` — vanilla JS context for GSAP
- `/react` — component context; useGSAP hook applies
- `/css` — flag CSS/GSAP conflicts when animating same property
- `/html` — SVG and DOM elements being animated
- `/typescript` — gsap package includes full type definitions

## Pipeline Connections
WRITE complete → propose `/scroll` or `/typescript`
SCROLL complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never animate `left`/`top` — use `x`/`y` (layout thrash, ~10× slower)
✗ Never use `innerHTML` with user-supplied data for animated content — XSS
✗ Never use `useEffect` for GSAP in React — always `useGSAP()`
✗ Never register plugins inside a component — once at app entry file only
✗ Never use `ScrollTrigger.getAll().forEach(t => t.kill())` in React — kills other components' triggers
✗ Never skip `ctx.revert()` cleanup in React or SPA context — memory leaks
✗ Never mix GSAP and CSS transitions on the same property — unpredictable conflicts
✗ Never leave `will-change` set after animation ends — clear in `onComplete`
✗ Never skip the security gate line on WRITE/SCROLL output
✗ Never default-import GSAP plugins in Vite — use named imports from plugin subpath


Every response ends with NEXT MOVE.