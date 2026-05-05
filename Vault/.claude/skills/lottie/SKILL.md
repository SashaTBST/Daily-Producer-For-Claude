---
name: lottie
description: Lottie animation builder covering the full design-to-dev pipeline — AE + Bodymovin export checklist, embedding with lottie-web / lottie-react / dotlottie-react, programmatic control, and file audits. Use when working with .json or .lottie animation files from After Effects or LottieFiles.
argument-hint: "[mode: export|embed|review|debug] [description] — add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Full pipeline: designer exports from AE → dev embeds in web → performance audit → debug.
Works with: `.json` (classic Lottie) and `.lottie` (dotLottie — zipped, 50–80% smaller).
Not for: Lottie iOS/Android native SDK, canvas drawing (use /javascript), SVG creation (use /svg-animation).

## Library Choice — State Context First
Ask which is in use before writing code. Default recommendations:
- **React (new project 2026):** `@lottiefiles/dotlottie-react` — WASM runtime, actively maintained, smaller files. Pre-1.0 — check changelog before upgrading.
- **React (existing lottie-react project):** `lottie-react` — stable, slower-moving, `.json` only.
- **Vanilla JS:** `lottie-web` — full control, all renderers, `.json` only without dotLottie wrapper.
Full comparison in REFERENCE.md.

## Modes

**EXPORT** — AE export checklist + broken file diagnosis.
Two scenarios: (1) you received a broken file — here's what to tell your designer. (2) You're doing the AE export yourself — follow this checklist.
Unsupported in Lottie — must fix before export: 3D layers, expressions on paths (bake to keyframes), plugin/third-party effects, time-remapped precomps with expressions. Blending modes: test — partial support varies by renderer.
Bodymovin key settings: disable 3D in comp settings, convert expressions to keyframes, remove plugin effects, embed images or use CDN URLs (avoid large Base64), run preview in Bodymovin before delivery.
Format choice: `.lottie` for production (50–80% smaller, multi-animation support, theming slots). `.json` for maximum tooling compatibility or simple one-off files.
NON-DEV: explain the AE → Bodymovin → file pipeline before listing checks.
End with: propose `/embed` once file is confirmed clean.

**EMBED** — Dev implementation: load, render, display.
Renderer choice: **SVG** (default — DOM-interactive, supports blur effects). **Canvas** (performance-first, CPU-bound, drops blur effects silently — if animation has blur, use SVG). **HTML** (avoid — limited feature support).
Performance tuning: `lottie.setQuality('medium')` or numeric (e.g. `2`) for Canvas on large files.
SSR (Next.js): `dynamic(() => import('lottie-react'), { ssr: false })` — Lottie uses `window`/`document` at render time.
SSR (Astro/Remix/Vite): guard with `typeof window !== 'undefined'` before instantiation or use lazy import.
Accessibility: always check `prefers-reduced-motion` before autoplay — full pattern in REFERENCE.md.
Control API: `play()` / `pause()` / `stop()` / `setSpeed(rate)` / `goToAndStop(frame, true)` / `playSegments([start, end], force)`.
dotlottie-react uses `dotLottieRefCallback` — different from lottie-react `LottieRef`. See REFERENCE.md for both patterns.
NON-DEV: explain renderer tradeoffs before writing code.
End with: propose `/review` for performance audit.

**REVIEW** — File and implementation audit. Report each: PASS / WARN / FAIL.
Check categories: (1) file size (under 100KB target; over 500KB = must optimize), (2) layer count and expression complexity, (3) renderer match (blur in animation → SVG not Canvas), (4) unsupported AE features, (5) SSR guard present, (6) prefers-reduced-motion check, (7) security — user-uploaded files.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** — Structured diagnosis for broken Lottie animations.
Open with these 4 intake questions before diagnosing:
  1. What should animate that isn't — or what looks wrong?
  2. Which library and renderer? (lottie-web / lottie-react / dotlottie-react + SVG/Canvas)
  3. Does the animation preview correctly on LottieFiles.com?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite a working animation to fix a scoped issue.

## Security Gate
Every EMBED response ends with this line — never skip it:
`Security pass: ✓ animation source is static/trusted (not user-uploaded) ✓ prefers-reduced-motion checked ✓ no user data fed into Lottie JSON properties ✓ SSR guard present if applicable`
Flag any item that cannot be confirmed — especially user-uploaded files: Lottie expression evaluator runs at parse time and is a known CVE surface. Stop and flag before presenting output.

## Cross-Skill Integration
- `/javascript` — vanilla JS context, WAAPI interplay, OffscreenCanvas for Canvas renderer
- `/react` — component context; SSR, dynamic import, ref patterns
- `/css` — prefers-reduced-motion media query
- `/svg-animation` — for CSS/WAAPI SVG work; Lottie outputs SVG DOM in SVG renderer
- `/html` — embedding context, `<div>` container setup

## Pipeline Connections
EXPORT clean → propose `/embed`
EMBED complete → propose `/review`
REVIEW clean → propose `/qa`

## Anti-patterns
✗ Never autoplay without checking `prefers-reduced-motion` — accessibility failure
✗ Never render user-uploaded Lottie files without validation — expression evaluator is a CVE surface
✗ Never use Canvas renderer when animation contains blur effects — silently dropped
✗ Never import lottie-web or lottie-react directly in Next.js without `ssr: false` — SSR crash
✗ Never embed large .json files (over 500KB) without optimizing first — use TinyLottie or LottieFiles optimizer
✗ Never rely on dotlottie-react API stability across minor versions — pre-1.0, check changelog
✗ Never leave 3D layers or unbaked expressions in the AE file — silently breaks on export
✗ Never use the HTML renderer — limited support, no active improvement
✗ Never skip the security gate line on EMBED output


Every response ends with NEXT MOVE.