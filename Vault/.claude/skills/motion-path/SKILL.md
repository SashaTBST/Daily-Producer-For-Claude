---
name: motion-path
description: Motion path animation builder covering CSS offset-path (the current spec — not motion-path), GSAP MotionPathPlugin, and anime-js createMotionPath. Use when animating an element along a curve, SVG path, or geometric route — icons, HUD elements, loaders, orbit effects.
argument-hint: "[mode: write|path|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Three approaches — this skill covers all:
- **CSS `offset-path`** — library-free, compositor-optimised, modern browsers (Safari partial)
- **GSAP MotionPath** — scroll-linked, sequenced, precise start/end control
- **anime-js `createMotionPath`** — lightweight sequencing via createTimeline
Not for: path creation (use Figma/Illustrator — see REFERENCE.md for export tips). Canvas path drawing (use /javascript).

## Naming — Non-Negotiable
`motion-path` is the old CSS property name — removed from Chrome M58+. Never produce it.
Current spec properties: `offset-path` / `offset-distance` / `offset-rotate` / `offset-anchor`.

## Modes

**WRITE** — CSS offset-path setup (library-free).
Define the path: `offset-path: path('M0,0 C100,0 200,100 300,0')` — SVG path string direct.
Safari fallback: `path()` has partial Safari support (iOS 16.2+, inconsistent). For Safari safety use `url(#svgPathId)` referencing an inline `<path>` element. Full browser support table in REFERENCE.md.
`offset-distance`: 0% = path start, 100% = path end. Always set `offset-distance: 0%` explicitly — element jumps to start position without it.
`offset-rotate: auto` — element rotates to follow path tangent. `auto reverse` = tangent + 180°. Explicit angle = constant rotation regardless of position.
`offset-anchor`: adjusts which point of the element sits on the path. Default `auto` = same as `transform-origin`. Override when element visual centre differs from its geometry (e.g. arrow tips).
Animate `offset-distance` with `@keyframes` or WAAPI. Do not animate `offset-path` itself — always compositor-only via `offset-distance`.
NON-DEV: explain the "bead on a wire" mental model before writing code.
End with: propose `/review` for browser support audit or `/path` for library control.

**PATH** — Library-assisted: GSAP MotionPath or anime-js createMotionPath.
Use when: scroll-linked motion, sequenced timelines, start/end partial traversal, or React context.
GSAP: `gsap.to(target, { motionPath: { path, align, alignOrigin: [0.5, 0.5], autoRotate: true, start: 0, end: 1, resolution: 40 } })`. `align` = element or selector to align path coordinates to. `resolution` default is 12 — increase to 40+ for smooth curves. `autoRotate: true` or degrees offset.
anime-js: `svg.createMotionPath(pathEl)` returns `{ translateX, translateY, rotate }` — spread directly into `animate()`. Path must be an SVG element reference, not a selector string alone.
Path coordinate mismatch: SVG `viewBox` scaling causes path coordinates to differ from page coordinates. Fix with GSAP `align` referencing the SVG element, or with `createMotionPath` using the path element from within the same SVG.
NON-DEV: explain which library to use and why before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — Browser support and performance audit. Report each: PASS / WARN / FAIL.
Check: (1) `motion-path` old property used, (2) Safari `path()` without fallback, (3) `offset-distance: 0%` explicit, (4) `offset-distance` animated (not `offset-path`), (5) GSAP resolution ≥ 40 for curves, (6) path coordinate system match, (7) `offset-anchor` correct for element type.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis.
Open with 4 intake questions:
  1. What should move along what path — and what's happening instead?
  2. CSS offset-path, GSAP MotionPath, or anime-js?
  3. Does the element appear at all, or is it invisible / in the wrong place?
  4. Is the path from an inline SVG or a path() string?
Common causes in REFERENCE.md. Never rewrite working animation to fix a scoped issue.

## Security Gate
Every WRITE and PATH response ends with this line — never skip it:
`Security pass: ✓ offset-path value is static (not user-supplied) ✓ SVG path id references are trusted ✓ no user data in path() string ✓ motion-path property not used`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/css` — @keyframes, WAAPI, offset-* property context
- `/gsap` — MotionPath plugin, ScrollTrigger integration for scroll-linked path motion
- `/anime-js` — createMotionPath spread pattern, createTimeline sequencing
- `/svg-animation` — SVG path element setup; transform-box fix applies to path-following elements too
- `/javascript` — WAAPI `.animate()` with offset-distance keyframes

## Pipeline Connections
WRITE complete → propose `/review`
PATH complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never use `motion-path` CSS property — removed from Chrome M58+; always `offset-path`
✗ Never omit `offset-distance: 0%` on the element — causes jump-to-start on animation
✗ Never animate `offset-path` directly — animate `offset-distance` only
✗ Never use `path()` in offset-path without a Safari fallback (`url(#id)`) for production
✗ Never leave GSAP `resolution` at default 12 for curved paths — pacing looks jerky
✗ Never pass a CSS selector string to anime-js `createMotionPath()` — requires actual SVG element reference
✗ Never ignore path coordinate system — SVG viewBox mismatch will displace animation
✗ Never pass user-supplied data into `path()` string — injection risk
✗ Never skip the security gate line on WRITE/PATH output


Every response ends with NEXT MOVE.