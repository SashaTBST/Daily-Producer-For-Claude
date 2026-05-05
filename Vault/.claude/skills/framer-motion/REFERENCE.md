# /framer-motion — Reference

## AnimatePresence Mode Table

| Mode | Behaviour | Use when |
|------|-----------|---------|
| `"sync"` (default) | Enter and exit simultaneously | Most transitions — modals, tooltips |
| `"wait"` | Exit completes before enter begins | Page transitions, one item at a time |
| `"popLayout"` | Exiting element removed from layout flow immediately | List removals — siblings reflow without waiting |

### Correct AnimatePresence structure
```tsx
// condition goes INSIDE AnimatePresence
<AnimatePresence mode="wait">
  {isVisible && (
    <motion.div
      key="modal"           // required — unique, stable
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: 20 }}  // required — or exit silently won't fire
      transition={{ type: 'tween', duration: 0.2 }}
    />
  )}
</AnimatePresence>
```

## Variants Pattern (with Stagger)

```tsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1,  // 100ms between each child
    },
  },
};

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0, transition: { type: 'tween' } },
};

<motion.ul variants={container} initial="hidden" animate="show">
  {items.map(i => (
    <motion.li key={i.id} variants={item}>{i.label}</motion.li>
  ))}
</motion.ul>
```
Children inherit `initial`/`animate` from parent when using variants — no need to repeat on each child.

## Layout Animation Patterns

### Basic layout
```tsx
// Animates size/position change when className or style changes
<motion.div layout className={isExpanded ? 'expanded' : 'collapsed'} />
```

### layoutId — shared element transition
```tsx
// In list view
<motion.div layoutId="card-thumbnail" />

// In detail view (different component, same layoutId)
<motion.div layoutId="card-thumbnail" />
// Motion animates between the two positions automatically
```

### LayoutGroup — fix global layoutId conflicts
```tsx
// Without LayoutGroup: two Card components share the same layoutId globally
// Result: they animate to each other's position on mount — visible jump
import { LayoutGroup } from 'motion/react';

<LayoutGroup id="cards-list">
  <Card />  {/* layoutId="card-thumbnail" scoped to this group */}
  <Card />  {/* layoutId="card-thumbnail" scoped to this group — no conflict */}
</LayoutGroup>
```

## Scroll Hooks

```tsx
import { useScroll, useTransform, useSpring } from 'motion/react';
import { useRef } from 'react';

function ParallaxSection() {
  const containerRef = useRef(null);

  // useScroll defaults to window — use container ref for overflow/modal contexts
  const { scrollYProgress } = useScroll({
    target: containerRef,
    offset: ['start end', 'end start'],  // [when element enters viewport, when it leaves]
  });

  // Map scroll 0→1 to opacity 0→1
  const opacity = useTransform(scrollYProgress, [0, 0.5], [0, 1]);

  // Smooth the value (spring-based)
  const smoothOpacity = useSpring(opacity, { stiffness: 100, damping: 30 });

  return (
    <div ref={containerRef}>
      <motion.div style={{ opacity: smoothOpacity }}>Content</motion.div>
    </div>
  );
}
```

## useAnimate Pattern

```tsx
import { useAnimate } from 'motion/react';

function Button() {
  const [scope, animate] = useAnimate();

  const handleClick = async () => {
    // animate() is scoped to elements inside scope ref
    await animate('span', { scale: 1.2 }, { duration: 0.1 });
    await animate('span', { scale: 1 }, { duration: 0.1 });
  };

  return (
    <div ref={scope}>
      <button onClick={handleClick}><span>Click me</span></button>
    </div>
  );
}
// Cleanup is automatic — animations stop when component unmounts
// Do NOT use deprecated useAnimation() — it has no automatic cleanup
```

## Next.js App Router Pattern

```tsx
// app/components/AnimatedCard.tsx
'use client';  // MUST be first line — before all imports

import { motion } from 'motion/react';

export function AnimatedCard({ title }: { title: string }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ type: 'tween', duration: 0.3 }}
    >
      {title}
    </motion.div>
  );
}

// In a Server Component — import the client component normally
// app/page.tsx (Server Component — no 'use client' here)
import { AnimatedCard } from './components/AnimatedCard';
export default function Page() {
  return <AnimatedCard title="Hello" />;
}
```

## framer-motion → motion Migration

| Old (`framer-motion`) | New (`motion`) | Notes |
|-----------------------|---------------|-------|
| `import { motion } from 'framer-motion'` | `import { motion } from 'motion/react'` | Drop-in |
| `import { useAnimation } from 'framer-motion'` | `import { useAnimate } from 'motion/react'` | API changed — see useAnimate pattern |
| `import { AnimateSharedLayout }` | Use `LayoutGroup` | AnimateSharedLayout removed |
| `import { LazyMotion, domAnimation }` | Still available | Unchanged |
| `useMotionValue`, `useTransform`, `useSpring` | Unchanged | Same API |

## Performance & Security Review Checklist

Report each: PASS / WARN / FAIL.

| # | Check | FAIL condition |
|---|-------|---------------|
| 1 | Layout-triggering properties | `width`, `height`, `top`, `left` in animate prop |
| 2 | Spring on opacity/color | `type: 'spring'` (or default) on opacity, color, background |
| 3 | useAnimate cleanup | `useAnimation` used instead of `useAnimate` |
| 4 | AnimatePresence key | Children missing stable `key` prop |
| 5 | AnimatePresence exit | Children missing `exit` prop |
| 6 | layoutId scope | Multiple component instances with same `layoutId` and no `LayoutGroup` |
| 7 | useScroll target | `useScroll()` used in overflow container without `container` ref |
| 8 | CSS transition conflict | CSS `transition` property on element also animated by motion |
| 9 | CSS variable injection | Unvalidated user input passed to CSS custom property values |
| 10 | CSP inline styles | motion uses inline styles — `style-src 'unsafe-inline'` or nonces required |

## DEBUG — Common Issues

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Exit animation doesn't fire | Missing `key` or `exit` prop | Add both to AnimatePresence children |
| Exit animation doesn't fire (2) | Condition outside AnimatePresence | Move condition inside AnimatePresence |
| Opacity animation looks bouncy | Default spring transition | Add `transition={{ type: 'tween' }}` |
| Layout jump on mount | `layoutId` without `LayoutGroup` | Wrap instances in `<LayoutGroup>` |
| Scroll progress always 0 | `useScroll` targeting window in overflow | Add `container: containerRef` to useScroll |
| Hydration mismatch | Server/client animation state differs | Wrap initial value in `useEffect` or use `suppressHydrationWarning` |
| Motion component not rendering | Missing `'use client'` in Next.js | Add `'use client'` as first line |
| Component animates to wrong position | `layoutId` global conflict | Wrap in `LayoutGroup` with unique `id` |

## Research Sources

1. https://motion.dev/docs/react-upgrade-guide — framer-motion → motion migration guide
2. https://motion.dev/docs/react-animate-presence — AnimatePresence official docs
3. https://motion.dev/tutorials/react-animate-presence-modes — AnimatePresence modes explained
4. https://motion.dev/docs/react-use-animate — useAnimate docs
5. https://motion.dev/docs/react-layout-group — LayoutGroup docs
6. https://motion.dev/docs/react-layout-animations — Layout animations docs
7. https://motion.dev/docs/performance — Performance guide
8. https://inhaq.com/blog/framer-motion-complete-guide-react-nextjs-developers — Next.js App Router patterns
9. https://github.com/framer/motion/issues/1727 — CSP inline styles issue
10. https://security.snyk.io/package/npm/framer-motion — Security audit
