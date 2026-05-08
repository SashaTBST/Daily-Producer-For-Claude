---
name: framer-motion
description: Motion (formerly Framer Motion) animation builder for React and Next.js. Covers motion components, AnimatePresence, layout animations, useAnimate, useScroll, useSpring, and shared element transitions. Use when adding gesture-responsive, exit, or layout animations to React components.
argument-hint: "[mode: write|animate|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Package
Renamed: `framer-motion` → `motion` (2025). `framer-motion` is deprecated — no longer actively developed.
New: `npm install motion`. Import: `import { motion, AnimatePresence } from 'motion/react'`.
Existing framer-motion project: replace import paths first — most APIs compatible. Breaking changes in REFERENCE.md.

## Scope
/framer-motion = React component animation: enter/exit, gestures, layout, scroll-linked, shared transitions.
Vanilla JS or complex timelines → `/gsap`. React component structure → `/react`.
Next.js App Router: `'use client'` required on any file using motion — server components cannot use motion.

## Modes

**WRITE** — motion components, initial/animate/exit, transitions, variants, gestures.
Next.js App Router: first line of every file using motion must be `'use client'` — add before imports.
Replace `<div>` with `<motion.div>`. Pass `initial`, `animate`, `exit` props for lifecycle animation.
Transitions: for opacity, color, background — use `transition={{ type: 'tween' }}`. Springs are for transforms, not property values — a spring on opacity looks bouncy and broken.
Variants: named animation state objects on `variants` prop, referenced by string in `animate`. Cleaner for complex components. Stagger pattern in REFERENCE.md.
Gestures: `whileHover`, `whileTap`, `whileFocus` — no JS event listeners needed.
`useAnimate`: use for imperative animations inside handlers or effects. Auto-cleanup on unmount. Never use deprecated `useAnimation`.
Never animate `width`/`height`/`top`/`left` — use the `layout` prop for size/position changes.
Never pass unvalidated user input to CSS custom property values — injection risk.
NON-DEV: plain-English comment on any prop or transition option.
End with: propose `/animate` for AnimatePresence or layout animations.

**ANIMATE** — AnimatePresence, layout animations, shared transitions, scroll hooks.
AnimatePresence: wraps the conditional block — `{isVisible && <motion.div>}` goes inside AnimatePresence, not outside. `mode`: `"wait"` (exit then enter), `"sync"` (simultaneous), `"popLayout"` (exit removed from flow). Both `key` and `exit` props required on children — missing either causes silent failure with no error.
Layout: `layout` prop animates size/position changes automatically. `layoutId` for shared element transitions between components. `layoutId` is global — without `LayoutGroup`, two instances animate to each other's position on mount (visible jump). Wrap in `LayoutGroup` to scope.
Scroll: `useScroll()` defaults to window — use `useScroll({ container: containerRef })` for modals, sidebars, overflow containers. `useTransform()` maps scroll progress to animation values. `useSpring()` smooths any motion value.
NON-DEV: explain layout vs layoutId before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — Performance and security audit. Report each: PASS / WARN / FAIL.
Check categories: (1) layout-triggering properties animated directly, (2) spring type on opacity/color, (3) useAnimate cleanup present, (4) security — CSS variable injection, unvalidated input in style props, CSP inline-style conflict.
If operator mentions CSP errors: motion requires `style-src 'unsafe-inline'` or nonces — flag and advise `/css` for header config.
Full per-item checklist in REFERENCE.md. End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis.
Open with 4 intake questions before diagnosing:
  1. What should animate that isn't? (expected vs actual)
  2. Are you using Next.js App Router?
  3. Any console errors or hydration warnings?
  4. Animation type: enter / exit / layout / scroll?
Branch on Q4 — exit: check AnimatePresence wrapper + key + exit prop; layout: check layout prop + LayoutGroup; enter: check initial/animate values; scroll: check useScroll container ref.
Common causes in REFERENCE.md. Targeted fix only — never rewrite working animation.

## Security Gate
Every WRITE and ANIMATE response ends with this line — never skip it:
`Security pass: ✓ no user input in CSS custom properties ✓ layout prop used for size/position ✓ useAnimate cleanup on unmount ✓ tween used for opacity/color`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/react` — motion is React-only; propose for component structure
- `/gsap` — for complex timelines, non-CSS values, vanilla JS animation
- `/css` — if CSS transition targets same property as motion: motion wins on mount, CSS wins after — flicker; remove the CSS transition
- `/typescript` — motion package includes full type definitions

## Pipeline Connections
WRITE complete → propose `/animate` or `/typescript`
ANIMATE complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never install `framer-motion` for new projects — use `motion`
✗ Never import from `'framer-motion'` in new code — use `'motion/react'`
✗ Never animate `width`/`height`/`top`/`left` — use `layout` prop
✗ Never skip `key` prop on AnimatePresence children — exit animations silently won't fire
✗ Never skip `exit` prop on AnimatePresence children — same result
✗ Never use `layoutId` without `LayoutGroup` when component renders in multiple places
✗ Never use deprecated `useAnimation` — use `useAnimate`
✗ Never use motion in Next.js App Router without `'use client'`
✗ Never pass unvalidated user input to CSS custom property values
✗ Never skip the security gate line on WRITE/ANIMATE output
