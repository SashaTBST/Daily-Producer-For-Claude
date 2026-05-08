---
name: motion-path
description: Motion path animation builder covering CSS offset-path (the current spec â€” not motion-path), GSAP MotionPathPlugin, and anime-js createMotionPath. Use when animating an element along a curve, SVG path, or geometric route â€” icons, HUD elements, loaders, orbit effects.
argument-hint: "[mode: write|path|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three approaches â€” this skill covers all:
- **CSS `offset-path`** â€” library-free, compositor-optimised, modern browsers (Safari partial)
- **GSAP MotionPath** â€” scroll-linked, sequenced, precise start/end control
- **anime-js `createMotionPath`** â€” lightweight sequencing via createTimeline
Not for: path creation (use Figma/Illustrator â€” see REFERENCE.md for export tips). Canvas path drawing (use /javascript).

## Naming â€” Non-Negotiable
`motion-path` is the old CSS property name â€” removed from Chrome M58+. Never produce it.
Current spec properties: `offset-path` / `offset-distance` / `offset-rotate` / `offset-anchor`.

## Modes

**WRITE** â€” CSS offset-path setup (library-free).
Define the path: `offset-path: path('M0,0 C100,0 200,100 300,0')` â€” SVG path string direct.
Safari fallback: `path()` has partial Safari support (iOS 16.2+, inconsistent). For Safari safety use `url(#svgPathId)` referencing an inline `<path>` element. Full browser support table in REFERENCE.md.
`offset-distance`: 0% = path start, 100% = path end. Always set `offset-distance: 0%` explicitly â€” element jumps to start position without it.
`offset-rotate: auto` â€” element rotates to follow path tangent. `auto reverse` = tangent + 180Â°. Explicit angle = constant rotation regardless of position.
`offset-anchor`: adjusts which point of the element sits on the path. Default `auto` = same as `transform-origin`. Override when element visual centre differs from its geometry (e.g. arrow tips).
Animate `offset-distance` with `@keyframes` or WAAPI. Do not animate `offset-path` itself â€” always compositor-only via `offset-distance`.
NON-DEV: explain the "bead on a wire" mental model before writing code.
End with: propose `/review` for browser support audit or `/path` for library control.

**PATH** â€” Library-assisted: GSAP MotionPath or anime-js createMotionPath.
Use when: scroll-linked motion, sequenced timelines, start/end partial traversal, or React context.
GSAP: `gsap.to(target, { motionPath: { path, align, alignOrigin: [0.5, 0.5], autoRotate: true, start: 0, end: 1, resolution: 40 } })`. `align` = element or selector to align path coordinates to. `resolution` default is 12 â€” increase to 40+ for smooth curves. `autoRotate: true` or degrees offset.
anime-js: `svg.createMotionPath(pathEl)` returns `{ translateX, translateY, rotate }` â€” spread directly into `animate()`. Path must be an SVG element reference, not a selector string alone.
Path coordinate mismatch: SVG `viewBox` scaling causes path coordinates to differ from page coordinates. Fix with GSAP `align` referencing the SVG element, or with `createMotionPath` using the path element from within the same SVG.
NON-DEV: explain which library to use and why before writing code.
End with: propose `/review` for performance audit.

**REVIEW** â€” Browser support and performance audit. Report each: PASS / WARN / FAIL.
Check: (1) `motion-path` old property used, (2) Safari `path()` without fallback, (3) `offset-distance: 0%` explicit, (4) `offset-distance` animated (not `offset-path`), (5) GSAP resolution â‰¥ 40 for curves, (6) path coordinate system match, (7) `offset-anchor` correct for element type.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis.
Open with 4 intake questions:
  1. What should move along what path â€” and what's happening instead?
  2. CSS offset-path, GSAP MotionPath, or anime-js?
  3. Does the element appear at all, or is it invisible / in the wrong place?
  4. Is the path from an inline SVG or a path() string?
Common causes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and PATH response ends with this line â€” never skip it:
`Security pass: âœ“ offset-path value is static (not user-supplied) âœ“ SVG path id references are trusted âœ“ no user data in path() string âœ“ motion-path property not used`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/css` â€” @keyframes, WAAPI, offset-* property context
- `/gsap` â€” MotionPath plugin, ScrollTrigger integration for scroll-linked path motion
- `/anime-js` â€” createMotionPath spread pattern, createTimeline sequencing
- `/svg-animation` â€” SVG path element setup; transform-box fix applies to path-following elements too
- `/javascript` â€” WAAPI `.animate()` with offset-distance keyframes

## Pipeline Connections
WRITE complete â†’ propose `/review`
PATH complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never use `motion-path` CSS property â€” removed from Chrome M58+; always `offset-path`
âœ— Never omit `offset-distance: 0%` on the element â€” causes jump-to-start on animation
âœ— Never animate `offset-path` directly â€” animate `offset-distance` only
âœ— Never use `path()` in offset-path without a Safari fallback (`url(#id)`) for production
âœ— Never leave GSAP `resolution` at default 12 for curved paths â€” pacing looks jerky
âœ— Never pass a CSS selector string to anime-js `createMotionPath()` â€” requires actual SVG element reference
âœ— Never ignore path coordinate system â€” SVG viewBox mismatch will displace animation
âœ— Never pass user-supplied data into `path()` string â€” injection risk
âœ— Never skip the security gate line on WRITE/PATH output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.