---
name: premium-web-animations
description: Copy-pasteable Framer Motion and CSS animation patterns for React. Scroll-triggered reveals, staggered lists, number counters, parallax, text scramble, and page transitions. Use when polishing a marketing website to feel premium and alive.
---

# Premium Web Animations

Production-ready animation patterns for React + Framer Motion that make websites feel alive and premium.

## Use this skill when

- Adding scroll-triggered entrance animations to marketing pages
- Building staggered list/card reveals
- Creating animated number counters or stat displays
- Implementing page/route transitions
- Adding parallax effects to hero sections
- Building text reveal or scramble animations
- Polishing hover and focus micro-interactions

## Do not use this skill when

- Building CRUD interfaces or dashboards (animations distract from data tasks)
- Performance is critically constrained (measure first!)
- The project doesn't use React

## Instructions

### Principle: Less Is More

Animations should be **subtle, purposeful, and fast**. The best animations are ones users feel but don't consciously notice. Rules:

- **Duration**: 300–600ms for entrances, 150–250ms for hover states
- **Easing**: Use spring physics (`type: "spring"`) over cubic bezier for organic feel
- **Stagger**: 50–100ms between children. Never more than 150ms.
- **Respect `prefers-reduced-motion`**: Always provide a no-animation fallback

---

### Pattern 1: Scroll-Triggered Fade-Up Reveal

The single most impactful animation for marketing sites. Elements fade up and in as they enter the viewport.

```tsx
import { motion, useReducedMotion } from "framer-motion";

interface ScrollRevealProps {
  children: React.ReactNode;
  className?: string;
  delay?: number;
  y?: number;
}

export function ScrollReveal({
  children,
  className,
  delay = 0,
  y = 24,
}: ScrollRevealProps) {
  const prefersReducedMotion = useReducedMotion();

  if (prefersReducedMotion) {
    return <div className={className}>{children}</div>;
  }

  return (
    <motion.div
      className={className}
      initial={{ opacity: 0, y }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: "-80px" }}
      transition={{
        duration: 0.5,
        delay,
        ease: [0.21, 0.47, 0.32, 0.98], // custom ease-out
      }}
    >
      {children}
    </motion.div>
  );
}
```

**Usage:** Wrap any section or card. `viewport.once: true` fires only once (no re-animate on scroll back).

---

### Pattern 2: Staggered Children (Cards, Lists, Grids)

Parent orchestrates children with a stagger delay. Each child animates in sequence.

```tsx
const containerVariants = {
  hidden: {},
  visible: {
    transition: {
      staggerChildren: 0.08,
      delayChildren: 0.1,
    },
  },
};

const itemVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.4, ease: "easeOut" },
  },
};

export function StaggeredGrid({ items }: { items: CardData[] }) {
  return (
    <motion.div
      className="grid grid-cols-1 md:grid-cols-3 gap-6"
      variants={containerVariants}
      initial="hidden"
      whileInView="visible"
      viewport={{ once: true, margin: "-50px" }}
    >
      {items.map((item) => (
        <motion.div key={item.id} variants={itemVariants}>
          <Card item={item} />
        </motion.div>
      ))}
    </motion.div>
  );
}
```

---

### Pattern 3: Animated Number Counter

For stat sections ("$2.3B in transactions", "150+ clients"). Uses spring physics for organic feel.

```tsx
import { useInView, useMotionValue, useSpring, motion } from "framer-motion";
import { useEffect, useRef } from "react";

interface AnimatedCounterProps {
  target: number;
  prefix?: string;
  suffix?: string;
  decimals?: number;
  duration?: number;
}

export function AnimatedCounter({
  target,
  prefix = "",
  suffix = "",
  decimals = 0,
  duration = 1.5,
}: AnimatedCounterProps) {
  const ref = useRef<HTMLSpanElement>(null);
  const isInView = useInView(ref, { once: true, margin: "-100px" });
  const motionValue = useMotionValue(0);
  const spring = useSpring(motionValue, {
    damping: 40,
    stiffness: 100,
    duration: duration * 1000,
  });

  useEffect(() => {
    if (isInView) {
      motionValue.set(target);
    }
  }, [isInView, target, motionValue]);

  useEffect(() => {
    const unsubscribe = spring.on("change", (latest) => {
      if (ref.current) {
        ref.current.textContent = prefix + latest.toFixed(decimals) + suffix;
      }
    });
    return unsubscribe;
  }, [spring, prefix, suffix, decimals]);

  return (
    <span ref={ref}>
      {prefix}0{suffix}
    </span>
  );
}
```

**Usage:** `<AnimatedCounter target={2.3} prefix="$" suffix="B" decimals={1} />`

---

### Pattern 4: Parallax Hero Section (CSS Only)

Lightweight parallax without JS libraries. Uses `transform: translateZ` with perspective.

```css
.parallax-container {
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  perspective: 1px;
  -webkit-overflow-scrolling: touch;
}

.parallax-bg {
  position: absolute;
  inset: -20% 0;
  transform: translateZ(-1px) scale(2);
  z-index: -1;
  will-change: transform;
}

.parallax-content {
  position: relative;
  transform: translateZ(0);
}
```

For Framer Motion approach with `useScroll`:

```tsx
import { motion, useScroll, useTransform } from "framer-motion";

export function ParallaxHero() {
  const { scrollYProgress } = useScroll();
  const y = useTransform(scrollYProgress, [0, 1], ["0%", "30%"]);

  return (
    <section className="relative h-screen overflow-hidden">
      <motion.div className="absolute inset-0 -z-10" style={{ y }}>
        <img src="/hero-bg.webp" className="h-full w-full object-cover" />
      </motion.div>
      <div className="relative z-10 flex items-center justify-center h-full">
        <h1>Your Headline</h1>
      </div>
    </section>
  );
}
```

---

### Pattern 5: Hover Micro-Interactions

Cards that lift on hover. Buttons that pulse. Links that underline-grow.

```css
/* Card lift — the most impactful 3 lines of CSS */
.card-hover {
  transition:
    transform 200ms ease,
    box-shadow 200ms ease;
}
.card-hover:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px -8px rgba(0, 0, 0, 0.15);
}

/* Link underline grow from left */
.link-underline {
  position: relative;
}
.link-underline::after {
  content: "";
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 0;
  height: 2px;
  background: currentColor;
  transition: width 250ms ease;
}
.link-underline:hover::after {
  width: 100%;
}

/* Button press effect */
.btn-press:active {
  transform: scale(0.97);
  transition: transform 100ms ease;
}

/* Gradient border shimmer */
.shimmer-border {
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.1),
    transparent
  );
  background-size: 200% 100%;
  animation: shimmer 2s infinite;
}
@keyframes shimmer {
  0% {
    background-position: -200% 0;
  }
  100% {
    background-position: 200% 0;
  }
}
```

---

### Pattern 6: Route/Page Transitions (React Router v7+)

```tsx
import { AnimatePresence, motion } from "framer-motion";
import { useLocation, useOutlet } from "react-router-dom";

const pageVariants = {
  initial: { opacity: 0, y: 8 },
  animate: { opacity: 1, y: 0 },
  exit: { opacity: 0, y: -8 },
};

export function AnimatedOutlet() {
  const location = useLocation();
  const outlet = useOutlet();

  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        variants={pageVariants}
        initial="initial"
        animate="animate"
        exit="exit"
        transition={{ duration: 0.25 }}
      >
        {outlet}
      </motion.div>
    </AnimatePresence>
  );
}
```

---

### Pattern 7: CSS `@starting-style` Entrance (No JS)

Modern CSS (Chrome 117+, Safari 17.4+) for dialog/popover entrance animations:

```css
dialog[open] {
  opacity: 1;
  transform: scale(1);
  transition:
    opacity 300ms ease,
    transform 300ms ease,
    display 300ms allow-discrete;

  @starting-style {
    opacity: 0;
    transform: scale(0.95);
  }
}
```

---

## Reduced Motion Support (Mandatory)

Always wrap animated components or use the `useReducedMotion` hook:

```tsx
import { useReducedMotion } from "framer-motion";

function AnimatedSection({ children }) {
  const prefersReducedMotion = useReducedMotion();

  return prefersReducedMotion ? (
    <div>{children}</div>
  ) : (
    <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }}>
      {children}
    </motion.div>
  );
}
```

Or globally via CSS:

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

## Performance Rules

- Never animate `width`, `height`, or `top/left` — use `transform` and `opacity` only
- Use `will-change: transform` sparingly (only on elements actively animating)
- Prefer CSS animations over JS for simple hover effects
- Use `viewport={{ once: true }}` for scroll-triggered animations
- Measure with Chrome DevTools Performance panel — target 60fps on mid-range mobile
