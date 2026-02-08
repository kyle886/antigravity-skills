---
name: responsive-polish-checklist
description: The "last 5%" cross-browser, cross-device quality checklist for production websites. Touch targets, safe areas, Safari gotchas, font rendering, aspect ratios, and container queries. Use before launch or when a site "works but doesn't feel right".
---

# Responsive Polish Checklist

The final 5% that separates amateur sites from professional ones. Concrete checks and fixes for cross-browser, cross-device quality.

## Use this skill when

- Preparing a website for production launch
- Something "feels off" but you can't pinpoint why
- Testing across devices reveals inconsistencies
- A client says "it looks weird on my iPhone/iPad"
- Running a pre-launch quality audit

## Do not use this skill when

- You're still in prototyping/MVP mode
- Performance is the primary concern (use `core-web-vitals-audit`)
- You need animation patterns (use `premium-web-animations`)

## Instructions

### Phase 1: Touch & Interactive Elements

**Minimum touch target: 44×44px** (WCAG 2.5.8, Apple HIG)

```css
/* Ensure all interactive elements meet minimum size */
button,
a,
[role="button"],
input,
select,
textarea {
  min-height: 44px;
  min-width: 44px;
}

/* For inline links that can't be 44px tall, add padding */
.inline-link {
  padding-block: 8px;
  margin-block: -8px; /* visual compensation */
}
```

Audit checklist:

- [ ] All buttons are at least 44×44px on mobile
- [ ] Nav links have adequate spacing (≥8px gap)
- [ ] Close/dismiss buttons are easy to tap (don't hide in corners)
- [ ] Form inputs are at least 44px tall
- [ ] Checkbox/radio tap areas include the label

---

### Phase 2: Safe Areas & Viewport

Handle notches, home indicators, and dynamic viewport height:

```css
/* Safe area padding for notched devices */
.full-width-section {
  padding-left: env(safe-area-inset-left, 0);
  padding-right: env(safe-area-inset-right, 0);
}

/* Fixed bottom bars must respect home indicator */
.fixed-bottom-bar {
  padding-bottom: env(safe-area-inset-bottom, 0);
}

/* Use dvh for full-height heroes (accounts for mobile browser chrome) */
.hero-fullscreen {
  min-height: 100dvh; /* dynamic viewport height */
}

/* Fallback for older browsers */
@supports not (min-height: 100dvh) {
  .hero-fullscreen {
    min-height: 100vh;
    min-height: -webkit-fill-available;
  }
}
```

Audit checklist:

- [ ] Hero sections use `100dvh` not `100vh`
- [ ] No content is clipped behind notches on iPhone
- [ ] Fixed navigation doesn't overlap with status bar
- [ ] Bottom-fixed elements clear the home indicator
- [ ] Landscape orientation doesn't break layout

---

### Phase 3: Text & Typography Polish

```css
/* Prevent orphans in headings */
h1,
h2,
h3 {
  text-wrap: balance;
}

/* Prevent orphans in body copy */
p {
  text-wrap: pretty;
}

/* Prevent text overflow everywhere */
.truncate-safe {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

/* Multi-line clamp */
.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* Optimal line length for readability */
.prose-width {
  max-width: 65ch;
}
```

Audit checklist:

- [ ] No text overflows its container at any viewport width
- [ ] Headings use `text-wrap: balance`
- [ ] Body text has max-width of 60–75ch for readability
- [ ] Font sizes are ≥16px on mobile (prevents iOS zoom on input focus)
- [ ] No tiny text below 12px anywhere

---

### Phase 4: Image & Media

```css
/* All images should be responsive by default */
img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* Prevent layout shift with aspect-ratio */
.hero-image {
  aspect-ratio: 16 / 9;
  object-fit: cover;
  width: 100%;
}

/* Skeleton placeholder to prevent CLS */
.image-skeleton {
  background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
}
```

Audit checklist:

- [ ] All images have explicit `width` and `height` attributes (or `aspect-ratio`)
- [ ] Hero images use `<picture>` with WebP/AVIF sources
- [ ] Images have descriptive `alt` text (not empty, not "image")
- [ ] Decorative images use `alt=""` and `aria-hidden="true"`
- [ ] No images are excessively large for their container (check with DevTools)
- [ ] SVG icons use `currentColor` for theming
- [ ] Favicon includes 32×32 PNG, 180×180 Apple touch icon, and SVG

---

### Phase 5: Safari-Specific Fixes

Safari is the new IE. Known gotchas:

```css
/* Fix 100vh on iOS Safari (bottom bar overlap) */
.full-height {
  min-height: 100dvh;
}

/* Fix sticky positioning in Safari */
.sticky-header {
  position: -webkit-sticky;
  position: sticky;
}

/* Fix smooth scrolling on iOS */
.scroll-container {
  -webkit-overflow-scrolling: touch;
  overscroll-behavior: contain;
}

/* Fix backdrop-filter rendering */
.glass-effect {
  -webkit-backdrop-filter: blur(12px);
  backdrop-filter: blur(12px);
}

/* Fix gap in flexbox (Safari 14.0 didn't support gap in flex) */
/* Modern Safari supports it — but verify your browser targets */
```

Audit checklist:

- [ ] `backdrop-filter` has `-webkit-` prefix
- [ ] `100dvh` is used instead of `100vh` for full-height sections
- [ ] Sticky elements work in scroll containers
- [ ] `overscroll-behavior` is set on scroll containers to prevent pull-to-refresh interference
- [ ] Date inputs render correctly (Safari doesn't support `type="datetime-local"` well)
- [ ] Smooth scroll works (Safari needs `scroll-behavior: smooth` on `html`, not `body`)

---

### Phase 6: Dark Mode Consistency

```css
/* Ensure all surfaces have explicit dark mode values */
:root {
  --surface-primary: hsl(0 0% 100%);
  --surface-secondary: hsl(220 14% 96%);
  --text-primary: hsl(220 14% 10%);
  --text-secondary: hsl(220 10% 40%);
  --border-default: hsl(220 13% 91%);
}

.dark {
  --surface-primary: hsl(224 71% 4%);
  --surface-secondary: hsl(220 14% 10%);
  --text-primary: hsl(210 40% 98%);
  --text-secondary: hsl(215 16% 60%);
  --border-default: hsl(215 28% 17%);
}
```

Audit checklist:

- [ ] Every surface has an explicit dark mode color (no white flashes)
- [ ] Images/illustrations work on dark backgrounds (check PNGs with white backgrounds)
- [ ] Box shadows are modified for dark mode (use lighter, more diffused shadows)
- [ ] Charts and graphs have dark-mode-aware color palettes
- [ ] OG/social images don't clash with dark mode (they're external)
- [ ] Color contrast meets WCAG AA (4.5:1 for body text, 3:1 for large text)

---

### Phase 7: Final Pre-Launch Checklist

- [ ] **Scroll**: No horizontal scroll at any viewport width (320px to 2560px)
- [ ] **Focus**: Tab order is logical, focus rings are visible
- [ ] **Links**: No broken links (`npx linkinator http://localhost:4173 --recurse`)
- [ ] **Console**: Zero errors in browser console on all pages
- [ ] **Print**: Basic print stylesheet exists (`@media print`)
- [ ] **Favicon**: Shows in all browsers and when bookmarked
- [ ] **OG Images**: Correct dimensions (1200×630), all pages have unique images
- [ ] **404 page**: Custom, branded, with navigation back to home
- [ ] **Loading**: No flash of unstyled content (FOUC)
- [ ] **Scroll to top**: Long pages have a "back to top" or reset scroll on route change
- [ ] **Footer**: Contains correct year, company info, legal links
