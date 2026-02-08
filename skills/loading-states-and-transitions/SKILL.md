---
name: loading-states-and-transitions
description: Production patterns for skeleton screens, progressive image loading, route transitions, optimistic UI, and suspense boundaries in React. Use when a site "works" but doesn't feel instant or polished during loading.
---

# Loading States & Transitions

What separates "works" from "feels fantastic" — patterns for every loading state in a React application.

## Use this skill when

- Users see blank white space while data loads
- Images pop in jarringly instead of fading
- Route changes feel abrupt
- Forms feel sluggish after submission
- You need skeleton screens for async content

## Do not use this skill when

- The app loads data synchronously (static site)
- You're optimizing server-side rendering (SSR/SSG)
- Performance is measured purely by Lighthouse metrics (use `core-web-vitals-audit`)

## Instructions

### Pattern 1: Skeleton Screens

Skeletons show the shape of content before it loads. They reduce perceived loading time by 30–40% compared to spinners.

```tsx
// Reusable skeleton primitive
function Skeleton({ className }: { className?: string }) {
  return <div className={cn("animate-pulse rounded-md bg-muted", className)} />;
}

// Card skeleton that matches real card layout
function CardSkeleton() {
  return (
    <div className="rounded-xl border p-6 space-y-4">
      <Skeleton className="h-6 w-3/4" /> {/* Title */}
      <Skeleton className="h-4 w-1/2" /> {/* Subtitle */}
      <div className="space-y-2">
        <Skeleton className="h-4 w-full" /> {/* Body line 1 */}
        <Skeleton className="h-4 w-5/6" /> {/* Body line 2 */}
      </div>
      <div className="flex gap-2 pt-2">
        <Skeleton className="h-9 w-24 rounded-md" /> {/* Button 1 */}
        <Skeleton className="h-9 w-20 rounded-md" /> {/* Button 2 */}
      </div>
    </div>
  );
}

// Usage — match skeleton count to expected items
function MarketCards() {
  const { data, isLoading } = useMarketData();

  if (isLoading) {
    return (
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        {Array.from({ length: 6 }).map((_, i) => (
          <CardSkeleton key={i} />
        ))}
      </div>
    );
  }

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      {data.map((market) => (
        <MarketCard key={market.id} data={market} />
      ))}
    </div>
  );
}
```

**Rules:**

- Skeleton shape MUST match the real content layout (same heights, widths, spacing)
- Use `animate-pulse` (Tailwind) or a shimmer gradient for the loading effect
- Show 3–6 skeleton items for grids; match the expected count
- Never mix skeletons with spinners on the same page

---

### Pattern 2: Progressive Image Loading (Blur-Up)

Show a tiny blurred placeholder that transitions to the full image.

```tsx
import { useState } from "react";

interface ProgressiveImageProps {
  src: string;
  alt: string;
  className?: string;
  aspectRatio?: string;
}

export function ProgressiveImage({
  src,
  alt,
  className,
  aspectRatio = "16 / 9",
}: ProgressiveImageProps) {
  const [loaded, setLoaded] = useState(false);

  return (
    <div className="relative overflow-hidden bg-muted" style={{ aspectRatio }}>
      {/* Shimmer placeholder */}
      {!loaded && <div className="absolute inset-0 animate-pulse bg-muted" />}

      <img
        src={src}
        alt={alt}
        loading="lazy"
        onLoad={() => setLoaded(true)}
        className={cn(
          "h-full w-full object-cover transition-opacity duration-500",
          loaded ? "opacity-100" : "opacity-0",
          className,
        )}
      />
    </div>
  );
}
```

**For hero images (above-fold):** Use `loading="eager"` and `fetchpriority="high"`.

---

### Pattern 3: Content Fade-In on Data Load

When data arrives, fade it in instead of popping it in:

```tsx
import { motion, AnimatePresence } from "framer-motion";

function DataSection({ data, isLoading }: { data: any; isLoading: boolean }) {
  return (
    <AnimatePresence mode="wait">
      {isLoading ? (
        <motion.div
          key="skeleton"
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          transition={{ duration: 0.15 }}
        >
          <CardSkeleton />
        </motion.div>
      ) : (
        <motion.div
          key="content"
          initial={{ opacity: 0, y: 8 }}
          animate={{ opacity: 1, y: 0 }}
          transition={{ duration: 0.3, ease: "easeOut" }}
        >
          <RealContent data={data} />
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```

---

### Pattern 4: Optimistic UI for Forms

Show success immediately, then sync with the server. Roll back on error.

```tsx
function ContactForm() {
  const [status, setStatus] = useState<"idle" | "sending" | "sent" | "error">(
    "idle",
  );

  const handleSubmit = async (data: FormData) => {
    setStatus("sending");

    // Optimistic: show success immediately
    setStatus("sent");

    try {
      await submitToServer(data);
      // Already showing "sent" — nothing to do
    } catch {
      setStatus("error");
      // Show error toast, allow retry
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* ... form fields ... */}
      <button type="submit" disabled={status === "sending"}>
        {status === "sending" && <Spinner className="mr-2" />}
        {status === "sent" ? "✓ Sent!" : "Send Message"}
      </button>
      {status === "error" && (
        <p className="text-destructive mt-2">
          Failed to send. Please try again.
        </p>
      )}
    </form>
  );
}
```

---

### Pattern 5: Suspense Boundaries with Fallbacks

React 18+ Suspense for lazy-loaded components:

```tsx
import { Suspense, lazy } from "react";

const MarketMap = lazy(() => import("@/components/MarketMap"));
const DataChart = lazy(() => import("@/components/DataChart"));

function MarketInsightsPage() {
  return (
    <div>
      <h1>Market Insights</h1>

      {/* Heavy map component with meaningful fallback */}
      <Suspense
        fallback={
          <div className="h-[500px] rounded-xl bg-muted animate-pulse flex items-center justify-center">
            <span className="text-muted-foreground">Loading map…</span>
          </div>
        }
      >
        <MarketMap />
      </Suspense>

      {/* Chart with skeleton fallback */}
      <Suspense fallback={<ChartSkeleton />}>
        <DataChart />
      </Suspense>
    </div>
  );
}
```

---

### Pattern 6: Scroll Position Restoration

Reset scroll on route change (SPA gotcha):

```tsx
import { useEffect } from "react";
import { useLocation } from "react-router-dom";

export function ScrollToTop() {
  const { pathname } = useLocation();

  useEffect(() => {
    globalThis.scrollTo({ top: 0, behavior: "instant" });
  }, [pathname]);

  return null;
}

// Mount in App.tsx
function App() {
  return (
    <Router>
      <ScrollToTop />
      <Routes>{/* ... */}</Routes>
    </Router>
  );
}
```

## Anti-Patterns

- **Full-page spinners**: Never block the entire page. Show content progressively.
- **"Loading..." text**: Use skeletons instead. Text feels slower than shapes.
- **Instant content pop**: Always fade/transition content in, even if it loaded instantly.
- **Missing error states**: Every loading state needs a corresponding error state.
- **Infinite skeletons**: Set a timeout (5–8s) — if still loading, show a retry option.
