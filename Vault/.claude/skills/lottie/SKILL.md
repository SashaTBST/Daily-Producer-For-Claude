---
name: lottie
description: Lottie animation builder covering the full design-to-dev pipeline â€” AE + Bodymovin export checklist, embedding with lottie-web / lottie-react / dotlottie-react, programmatic control, and file audits. Use when working with .json or .lottie animation files from After Effects or LottieFiles.
argument-hint: "[mode: export|embed|review|debug] [description] â€” add 'raw' for clean output without narration"
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
Full pipeline: designer exports from AE â†’ dev embeds in web â†’ performance audit â†’ debug.
Works with: `.json` (classic Lottie) and `.lottie` (dotLottie â€” zipped, 50â€“80% smaller).
Not for: Lottie iOS/Android native SDK, canvas drawing (use /javascript), SVG creation (use /svg-animation).

## Library Choice â€” State Context First
Ask which is in use before writing code. Default recommendations:
- **React (new project 2026):** `@lottiefiles/dotlottie-react` â€” WASM runtime, actively maintained, smaller files. Pre-1.0 â€” check changelog before upgrading.
- **React (existing lottie-react project):** `lottie-react` â€” stable, slower-moving, `.json` only.
- **Vanilla JS:** `lottie-web` â€” full control, all renderers, `.json` only without dotLottie wrapper.
Full comparison in REFERENCE.md.

## Modes

**EXPORT** â€” AE export checklist + broken file diagnosis.
Two scenarios: (1) you received a broken file â€” here's what to tell your designer. (2) You're doing the AE export yourself â€” follow this checklist.
Unsupported in Lottie â€” must fix before export: 3D layers, expressions on paths (bake to keyframes), plugin/third-party effects, time-remapped precomps with expressions. Blending modes: test â€” partial support varies by renderer.
Bodymovin key settings: disable 3D in comp settings, convert expressions to keyframes, remove plugin effects, embed images or use CDN URLs (avoid large Base64), run preview in Bodymovin before delivery.
Format choice: `.lottie` for production (50â€“80% smaller, multi-animation support, theming slots). `.json` for maximum tooling compatibility or simple one-off files.
NON-DEV: explain the AE â†’ Bodymovin â†’ file pipeline before listing checks.
End with: propose `/embed` once file is confirmed clean.

**EMBED** â€” Dev implementation: load, render, display.
Renderer choice: **SVG** (default â€” DOM-interactive, supports blur effects). **Canvas** (performance-first, CPU-bound, drops blur effects silently â€” if animation has blur, use SVG). **HTML** (avoid â€” limited feature support).
Performance tuning: `lottie.setQuality('medium')` or numeric (e.g. `2`) for Canvas on large files.
SSR (Next.js): `dynamic(() => import('lottie-react'), { ssr: false })` â€” Lottie uses `window`/`document` at render time.
SSR (Astro/Remix/Vite): guard with `typeof window !== 'undefined'` before instantiation or use lazy import.
Accessibility: always check `prefers-reduced-motion` before autoplay â€” full pattern in REFERENCE.md.
Control API: `play()` / `pause()` / `stop()` / `setSpeed(rate)` / `goToAndStop(frame, true)` / `playSegments([start, end], force)`.
dotlottie-react uses `dotLottieRefCallback` â€” different from lottie-react `LottieRef`. See REFERENCE.md for both patterns.
NON-DEV: explain renderer tradeoffs before writing code.
End with: propose `/review` for performance audit.

**REVIEW** â€” File and implementation audit. Report each: PASS / WARN / FAIL.
Check categories: (1) file size (under 100KB target; over 500KB = must optimize), (2) layer count and expression complexity, (3) renderer match (blur in animation â†’ SVG not Canvas), (4) unsupported AE features, (5) SSR guard present, (6) prefers-reduced-motion check, (7) security â€” user-uploaded files.
Full checklist in REFERENCE.md.
End with: propose `/qa` when clean.

**DEBUG** â€” Structured diagnosis for broken Lottie animations.
Open with these 4 intake questions before diagnosing:
  1. What should animate that isn't â€” or what looks wrong?
  2. Which library and renderer? (lottie-web / lottie-react / dotlottie-react + SVG/Canvas)
  3. Does the animation preview correctly on LottieFiles.com?
  4. Any console errors? (paste them)
Common causes and fixes in REFERENCE.md. Never rewrite a working animation to fix a scoped issue.

## Security Gate
Every EMBED response ends with this line â€” never skip it:
`Security pass: âœ“ animation source is static/trusted (not user-uploaded) âœ“ prefers-reduced-motion checked âœ“ no user data fed into Lottie JSON properties âœ“ SSR guard present if applicable`
Flag any item that cannot be confirmed â€” especially user-uploaded files: Lottie expression evaluator runs at parse time and is a known CVE surface. Stop and flag before presenting output.

## Cross-Skill Integration
- `/javascript` â€” vanilla JS context, WAAPI interplay, OffscreenCanvas for Canvas renderer
- `/react` â€” component context; SSR, dynamic import, ref patterns
- `/css` â€” prefers-reduced-motion media query
- `/svg-animation` â€” for CSS/WAAPI SVG work; Lottie outputs SVG DOM in SVG renderer
- `/html` â€” embedding context, `<div>` container setup

## Pipeline Connections
EXPORT clean â†’ propose `/embed`
EMBED complete â†’ propose `/review`
REVIEW clean â†’ propose `/qa`

## Anti-patterns
âœ— Never autoplay without checking `prefers-reduced-motion` â€” accessibility failure
âœ— Never render user-uploaded Lottie files without validation â€” expression evaluator is a CVE surface
âœ— Never use Canvas renderer when animation contains blur effects â€” silently dropped
âœ— Never import lottie-web or lottie-react directly in Next.js without `ssr: false` â€” SSR crash
âœ— Never embed large .json files (over 500KB) without optimizing first â€” use TinyLottie or LottieFiles optimizer
âœ— Never rely on dotlottie-react API stability across minor versions â€” pre-1.0, check changelog
âœ— Never leave 3D layers or unbaked expressions in the AE file â€” silently breaks on export
âœ— Never use the HTML renderer â€” limited support, no active improvement
âœ— Never skip the security gate line on EMBED output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.