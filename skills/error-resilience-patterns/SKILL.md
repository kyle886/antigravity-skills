---
name: error-resilience-patterns
description: Error boundaries, network retry, graceful degradation, and meaningful fallback UIs for React applications. Use when a site "breaks" on bad data, flaky networks, or edge cases — and you need it to "just work".
---

# Error Resilience Patterns

Make your React app "just work" by handling every failure gracefully. Users should never see a white screen.

## Use this skill when

- Users report blank screens or broken pages
- API/Supabase calls fail silently
- Images or assets fail to load
- Edge cases crash components
- Building for unreliable networks (mobile, conference wifi)
- Preparing for production launch (defensive coding)

## Do not use this skill when

- Debugging the root cause of failures (use `debugging-strategies`)
- Handling server-side errors (use `error-handling-patterns`)

## Instructions

### Pattern 1: Component Error Boundary

Catch render errors and show a meaningful fallback instead of a white screen.

```tsx
import { Component, type ErrorInfo, type ReactNode } from "react";

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
  onError?: (error: Error, errorInfo: ErrorInfo) => void;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error("ErrorBoundary caught:", error, errorInfo);
    this.props.onError?.(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        this.props.fallback ?? (
          <div className="flex flex-col items-center justify-center py-16 text-center">
            <h2 className="text-lg font-semibold">Something went wrong</h2>
            <p className="mt-2 text-muted-foreground max-w-md">
              We're sorry — this section encountered an error. Try refreshing
              the page.
            </p>
            <button
              onClick={() => this.setState({ hasError: false })}
              className="mt-4 px-4 py-2 rounded-md bg-primary text-primary-foreground"
            >
              Try Again
            </button>
          </div>
        )
      );
    }
    return this.props.children;
  }
}
```

**Usage:** Wrap sections, not the whole app. Each major section gets its own boundary:

```tsx
function MarketInsightsPage() {
  return (
    <div>
      <ErrorBoundary fallback={<p>Failed to load market map.</p>}>
        <MarketMap />
      </ErrorBoundary>

      <ErrorBoundary fallback={<p>Failed to load charts.</p>}>
        <MarketCharts />
      </ErrorBoundary>
    </div>
  );
}
```

---

### Pattern 2: Safe Data Access

Never trust API data shapes. Defensive access patterns:

```tsx
// BAD: crashes on null/undefined
const name = data.building.name;

// GOOD: optional chaining + fallback
const name = data?.building?.name ?? "Unknown Building";

// BAD: crashes on non-array
data.markets.map((m) => m.name);

// GOOD: defensive array handling
(Array.isArray(data?.markets) ? data.markets : []).map((m) => m.name);

// Utility for safe number formatting
function safeFormat(value: unknown, fallback = "N/A"): string {
  if (value == null || value === "" || Number.isNaN(Number(value))) {
    return fallback;
  }
  return Number(value).toLocaleString();
}
```

---

### Pattern 3: Network Error Handling with Retry

Wrap fetch calls with retry logic and user-facing error states:

```tsx
async function fetchWithRetry<T>(
  url: string,
  options?: RequestInit,
  retries = 3,
  delay = 1000,
): Promise<T> {
  for (let attempt = 1; attempt <= retries; attempt++) {
    try {
      const response = await fetch(url, options);
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }
      return await response.json();
    } catch (error) {
      if (attempt === retries) throw error;
      await new Promise((r) => setTimeout(r, delay * attempt));
    }
  }
  throw new Error("Unreachable");
}
```

For React Query / TanStack Query (built-in retry):

```tsx
const { data, error, isLoading, refetch } = useQuery({
  queryKey: ["markets"],
  queryFn: () => fetchMarkets(),
  retry: 3,
  retryDelay: (attempt) => Math.min(1000 * 2 ** attempt, 10000),
  staleTime: 5 * 60 * 1000, // 5 minutes
});

// Show error with retry button
if (error) {
  return (
    <div className="text-center py-8">
      <p className="text-destructive">Failed to load market data</p>
      <Button variant="outline" onClick={() => refetch()} className="mt-4">
        Retry
      </Button>
    </div>
  );
}
```

---

### Pattern 4: Image Error Fallbacks

Images fail silently — broken image icons look unprofessional:

```tsx
function SafeImage({
  src,
  alt,
  fallbackSrc = "/images/placeholder.webp",
  ...props
}: React.ImgHTMLAttributes<HTMLImageElement> & { fallbackSrc?: string }) {
  const [imgSrc, setImgSrc] = useState(src);
  const [hasError, setHasError] = useState(false);

  return (
    <img
      {...props}
      src={hasError ? fallbackSrc : imgSrc}
      alt={alt}
      onError={() => {
        if (!hasError) {
          setHasError(true);
          setImgSrc(fallbackSrc);
        }
      }}
    />
  );
}
```

---

### Pattern 5: Graceful Degradation Tiers

Not all features need the same reliability level. Categorize:

| Tier            | Examples                                | Failure Behavior                                    |
| --------------- | --------------------------------------- | --------------------------------------------------- |
| **Critical**    | Navigation, contact form, core content  | Never fail. Static fallback content if API is down. |
| **Important**   | Market data, charts, maps               | Show skeleton → error state with retry.             |
| **Enhancement** | Animations, live counters, chat widgets | Fail silently. Remove from DOM on error.            |

```tsx
// Enhancement tier: fail silently
function EnhancementBoundary({ children }: { children: ReactNode }) {
  return <ErrorBoundary fallback={null}>{children}</ErrorBoundary>;
}

// Important tier: show error UI
function ImportantBoundary({
  children,
  label,
}: {
  children: ReactNode;
  label: string;
}) {
  return (
    <ErrorBoundary
      fallback={
        <div className="rounded-lg border border-dashed border-muted p-8 text-center">
          <p className="text-muted-foreground">Unable to load {label}</p>
        </div>
      }
    >
      {children}
    </ErrorBoundary>
  );
}
```

---

### Pattern 6: 404 Page That Helps

```tsx
function NotFoundPage() {
  return (
    <div className="min-h-[60dvh] flex flex-col items-center justify-center text-center px-4">
      <h1 className="text-6xl font-bold text-primary">404</h1>
      <p className="mt-4 text-xl text-muted-foreground">
        This page doesn't exist or has moved.
      </p>
      <div className="mt-8 flex gap-4">
        <Button asChild>
          <a href="/">Go Home</a>
        </Button>
        <Button variant="outline" asChild>
          <a href="/contact">Contact Us</a>
        </Button>
      </div>
    </div>
  );
}
```

## Pre-Launch Resilience Checklist

- [ ] Every page has an ErrorBoundary (test by throwing in a component)
- [ ] All API calls have error handling and user-facing error states
- [ ] All images have error fallbacks
- [ ] Network offline state is handled (show banner or cached content)
- [ ] No `undefined` access crashes in console on any page
- [ ] 404 page is custom and branded
- [ ] Form submissions handle failure with retry options
- [ ] Console is free of unhandled promise rejections
