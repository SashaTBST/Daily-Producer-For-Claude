# /lottie — Reference

## Library Comparison

| Library | Package | Version | Format | Runtime | Best for |
|---------|---------|---------|--------|---------|---------|
| lottie-web | `lottie-web` | 5.13.0 | .json | JS | Vanilla JS, full renderer control |
| lottie-react | `lottie-react` | 2.4.1 | .json | JS | Existing React projects |
| dotlottie-react | `@lottiefiles/dotlottie-react` | 0.17.12 | .json + .lottie | WASM | New React projects 2026 |

Install:
```
npm install lottie-web
npm install lottie-react
npm install @lottiefiles/dotlottie-react
```

**dotlottie-react note:** Pre-1.0 — actively maintained but API may change across minor versions. Check changelog before upgrading.

## Renderer Comparison

| Renderer | Quality | GPU | Blur support | DOM-interactive | Use when |
|----------|---------|-----|-------------|----------------|---------|
| SVG | Highest | Partial (blur) | ✓ | ✓ | Default — most animations |
| Canvas | Good | CPU-bound | ✗ (dropped silently) | ✗ | Performance-heavy; many elements; no blur |
| HTML | Limited | No | Partial | ✓ | Avoid — limited active support |

Canvas performance tuning: `lottie.setQuality('medium')` or numeric `2`–`5` — reduces accuracy slightly, significant perf gain on large files.

## .json vs .lottie Format

| Feature | .json | .lottie |
|---------|-------|---------|
| File size | Baseline | 50–80% smaller |
| Assets | Embedded as Base64 | Bundled in ZIP |
| Multi-animation | No | Yes (manifest) |
| Runtime theming | No | Yes (slots) |
| Tooling support | Universal | Growing (dotLottie spec) |
| Recommended for | Compatibility | Production 2026 |

## After Effects — Unsupported Features

| Feature | Status | Fix |
|---------|--------|-----|
| 3D layers | ✗ Unsupported | Flatten to 2D |
| 3D rotation/scale | ✗ Unsupported | Use 2D equivalents |
| Expressions on paths | ⚠ Partial | Bake to keyframes |
| Plugin / third-party effects | ✗ Unsupported | Remove or pre-render |
| Time-remapped precomps + expressions | ✗ Unsupported | Flatten precomp |
| Blending modes | ⚠ Partial | Test per renderer — varies by AE version |
| Shape layer fills/strokes | ✓ Supported | — |
| Simple keyframe animation | ✓ Supported | — |
| Masks | ✓ Supported | — |
| Wiggle (shape props only) | ⚠ Partial | Bake if complex |

**Bodymovin export checklist:**
- [ ] 3D disabled in comp settings
- [ ] All expressions baked to keyframes
- [ ] Plugin effects removed
- [ ] Images embedded or CDN URL (no large Base64 unless small file)
- [ ] Preview in Bodymovin player before delivery
- [ ] File size checked (target under 100KB; optimize if over 500KB)

## lottie-react — LottieRef Control Pattern

```tsx
import Lottie, { LottieRefCurrentProps } from 'lottie-react';
import { useRef } from 'react';
import animationData from './animation.json';

export function AnimatedIcon() {
  const lottieRef = useRef<LottieRefCurrentProps>(null);

  const handlePlay = () => lottieRef.current?.play();
  const handlePause = () => lottieRef.current?.pause();
  const handleSeek = () => lottieRef.current?.goToAndStop(30, true); // frame 30
  const handleSpeed = () => lottieRef.current?.setSpeed(1.5);
  const handleSegment = () => lottieRef.current?.playSegments([0, 60], true); // frames 0–60

  return (
    <Lottie
      lottieRef={lottieRef}
      animationData={animationData}
      loop={false}
      autoplay={false} // control manually
    />
  );
}
```

## dotlottie-react — dotLottieRefCallback Control Pattern

```tsx
import { DotLottieReact } from '@lottiefiles/dotlottie-react';
import { DotLottie } from '@lottiefiles/dotlottie-react';
import { useCallback, useRef } from 'react';

export function AnimatedIcon() {
  const dotLottieRef = useRef<DotLottie | null>(null);

  const refCallback = useCallback((dotLottie: DotLottie) => {
    dotLottieRef.current = dotLottie;
  }, []);

  const handlePlay = () => dotLottieRef.current?.play();
  const handlePause = () => dotLottieRef.current?.pause();
  const handleSeek = () => { dotLottieRef.current?.setFrame(30); dotLottieRef.current?.play(); }
  const handleSpeed = () => dotLottieRef.current?.setSpeed(1.5);

  return (
    <DotLottieReact
      dotLottieRefCallback={refCallback}
      src="/animation.lottie"
      loop={false}
      autoplay={false}
    />
  );
}
```

**Note:** dotlottie-react uses `setFrame()` + `play()` for segment-like control — no direct `playSegments` equivalent. For precise segment control, use lottie-react.

## Next.js / SSR Pattern

```tsx
// Next.js — pages or app router
import dynamic from 'next/dynamic';

const Lottie = dynamic(() => import('lottie-react'), { ssr: false });
// or
const DotLottieReact = dynamic(
  () => import('@lottiefiles/dotlottie-react').then(m => ({ default: m.DotLottieReact })),
  { ssr: false }
);
```

```tsx
// Astro / Remix / Vite SSR — conditional guard
let LottieComponent = null;
if (typeof window !== 'undefined') {
  const { default: Lottie } = await import('lottie-react');
  LottieComponent = Lottie;
}
```

## prefers-reduced-motion Pattern

```tsx
import { useEffect, useRef } from 'react';
import Lottie, { LottieRefCurrentProps } from 'lottie-react';
import animationData from './animation.json';

export function AccessibleAnimation() {
  const lottieRef = useRef<LottieRefCurrentProps>(null);

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)');
    if (mediaQuery.matches) {
      lottieRef.current?.goToAndStop(0, true); // freeze on first frame
    } else {
      lottieRef.current?.play();
    }
  }, []);

  return (
    <Lottie
      lottieRef={lottieRef}
      animationData={animationData}
      autoplay={false} // always false — control in useEffect
      loop={true}
    />
  );
}
```

CSS fallback (always include alongside JS pattern):
```css
@media (prefers-reduced-motion: reduce) {
  .lottie-container { animation: none !important; }
}
```

## lottie-web Vanilla Setup

```js
import lottie from 'lottie-web';

const animation = lottie.loadAnimation({
  container: document.getElementById('lottie-container'),
  renderer: 'svg',           // 'svg' | 'canvas' | 'html'
  loop: true,
  autoplay: false,
  path: '/animations/hero.json', // or animationData: jsonObject
});

// Control
animation.play();
animation.pause();
animation.stop();
animation.setSpeed(1.5);
animation.goToAndStop(30, true);      // frame 30, isFrame: true
animation.playSegments([0, 60], true); // force: true = reset first
animation.setDirection(-1);           // -1 = reverse

// Cleanup
animation.destroy();
```

## File Optimization

| Size | Status | Action |
|------|--------|--------|
| Under 100KB | ✓ Good | Ship as-is |
| 100–500KB | ⚠ Warn | Consider optimization |
| Over 500KB | ✗ Fail | Must optimize before shipping |

Tools: LottieFiles Optimizer (lottiefiles.com/tools/optimizer), TinyLottie.
Common bloat causes: embedded Base64 images, high-precision keyframe data, too many layers (200+).

## Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | File size | Over 500KB unoptimized |
| 2 | Layer count | Over 200 layers without Canvas renderer |
| 3 | Blur + renderer | Blur in animation + Canvas renderer (silently dropped) |
| 4 | Unsupported AE features | 3D layers, plugin effects, unbaked expressions |
| 5 | SSR guard | lottie-react/dotlottie-react in SSR context without `ssr: false` |
| 6 | Reduced motion | Autoplay without `prefers-reduced-motion` check |
| 7 | User-uploaded files | Unvalidated user Lottie JSON rendered (expression evaluator risk) |
| 8 | API version | dotlottie-react upgraded without changelog check |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| "document is not defined" (Next.js) | Missing `ssr: false` | `dynamic(() => import('lottie-react'), { ssr: false })` |
| Blank container, no error | Container element not found | Check DOM mount timing; ensure `ref` is attached |
| Animation plays but blur missing | Canvas renderer | Switch to SVG renderer |
| Export from AE is wrong/broken | 3D layers or unbaked expressions | Run EXPORT mode checklist |
| Animation previews fine on LottieFiles but broken in dev | Wrong renderer or unsupported feature | Check renderer; run REVIEW mode |
| `playSegments` has no effect | `force` param false + animation not reset | Pass `true` as second argument |
| dotlottie-react method not found | API changed in minor version | Check changelog; methods differ from lottie-react |
| Animation autoplays despite `autoplay: false` | `autoplay` prop ignored | Confirm prop spelling; check library version |

## Research Sources

1. https://www.npmjs.com/package/lottie-web — v5.13.0
2. https://www.npmjs.com/package/lottie-react — v2.4.1
3. https://www.npmjs.com/package/@lottiefiles/dotlottie-react — v0.17.12
4. https://github.com/airbnb/lottie-web/wiki/Renderer-Settings — renderer options
5. https://lottiefiles.com — platform overview and optimizer
6. https://dotlottie.io/intro/ — dotLottie spec
7. https://lottiefiles.github.io/lottie-player/methods.html — control API
8. https://lottiefiles.com/blog/working-with-lottie-animations/optimize-lottie-files-for-faster-page-load-speeds
9. https://forum.lottiefiles.com/t/json-with-gzip-vs-dotlottie/4128 — format comparison
10. https://medium.com/@emailsolah/lottie-react-nextjs-unhandled-runtime-error-error-document-is-not-defined-fix-8f4df29be872
