---
name: core-web-vitals-audit
description: Audit and optimize Core Web Vitals (LCP, CLS, INP) using Lighthouse, CrUX data, and PageSpeed Insights. Use when diagnosing performance regressions, preparing for production launch, or optimizing for Google search ranking signals.
---

# Core Web Vitals Audit

Comprehensive audit and optimization guide for the three Core Web Vitals metrics that affect Google search ranking.

## Use this skill when

- Running performance audits before production launch
- Diagnosing slow page loads, layout shifts, or unresponsive interactions
- Optimizing for Google's page experience ranking signals
- Setting up continuous performance monitoring
- Comparing performance across deployments

## Do not use this skill when

- You only need general bundle size optimization (use `performance-engineer` instead)
- You need visual/design feedback rather than performance metrics
- The site is not web-based (native apps, CLI tools)

## Instructions

### Phase 1: Measure

1. Run Lighthouse CI on the target URLs (both mobile and desktop):

```bash
npx lighthouse <url> --output=json --output=html --output-path=./audit-report --chrome-flags="--headless" --preset=desktop
npx lighthouse <url> --output=json --output=html --output-path=./audit-report-mobile --chrome-flags="--headless"
```

2. Extract the three Core Web Vitals from the JSON report:
   - **LCP** (Largest Contentful Paint): Target < 2.5s
   - **CLS** (Cumulative Layout Shift): Target < 0.1
   - **INP** (Interaction to Next Paint): Target < 200ms

3. Check Google CrUX data for real-world field data (if available):

```bash
curl "https://chromeuxreport.googleapis.com/v1/records:queryRecord?key=<API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"url": "<url>"}'
```

### Phase 2: Diagnose

4. For **LCP issues**:
   - Identify the LCP element (usually a hero image or heading)
   - Check for: render-blocking resources, slow server response, unoptimized images
   - Verify preload hints are working (`<link rel="preload">`)
   - Check that critical CSS is inlined or loaded early

5. For **CLS issues**:
   - Look for elements without explicit dimensions (images, ads, embeds)
   - Check for dynamically injected content above the fold
   - Verify font loading strategy (FOUT/FOIT)
   - Check lazy-loaded components for height reservation

6. For **INP issues**:
   - Profile JavaScript execution during interactions
   - Look for long tasks (>50ms) in the Performance panel
   - Check for expensive re-renders (React Profiler)
   - Verify event handlers aren't blocking the main thread

### Phase 3: Optimize

7. Apply fixes ordered by impact:
   - Image optimization (WebP/AVIF, responsive sizes, lazy loading)
   - Font loading strategy (`font-display: swap`, preload)
   - Code splitting for non-critical JavaScript
   - Skeleton screens for async content
   - `will-change` CSS for animated elements
   - `requestIdleCallback` for non-critical work

### Phase 4: Verify

8. Re-run Lighthouse and compare before/after scores
9. Document improvements with screenshots and metrics
10. Set up monitoring (if not already): Google Search Console → Core Web Vitals report

## Safety

- Do not modify code without understanding the visual impact of changes
- Test layout changes across breakpoints (mobile, tablet, desktop)
- Verify that optimization doesn't break functionality (especially lazy loading)

## Scoring Reference

| Metric | Good    | Needs Improvement | Poor    |
| ------ | ------- | ----------------- | ------- |
| LCP    | ≤ 2.5s  | 2.5s – 4.0s       | > 4.0s  |
| CLS    | ≤ 0.1   | 0.1 – 0.25        | > 0.25  |
| INP    | ≤ 200ms | 200ms – 500ms     | > 500ms |
