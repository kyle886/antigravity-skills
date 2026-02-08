---
name: typography-and-font-optimization
description: Font loading strategies, responsive type scales, text rendering optimization, and typographic polish for web. Use when text "looks off", fonts flash, or typography doesn't feel premium.
---

# Typography & Font Optimization

The foundation of professional feel. Font loading, type scales, rendering, and typographic details that elevate a website.

## Use this skill when

- Fonts flash (FOUT) or are invisible during loading (FOIT)
- Typography looks inconsistent across pages or breakpoints
- Text is hard to read or line lengths are too long
- You want to establish a systematic type scale
- Switching to variable fonts or optimizing font delivery

## Do not use this skill when

- You need general CSS help (use frontend skills)
- Working on icon fonts (use SVG icons instead)

## Instructions

### Phase 1: Font Loading Strategy

The single biggest typography issue on the web. Prevent Flash of Unstyled Text (FOUT) and Flash of Invisible Text (FOIT).

**Recommended approach:** `font-display: swap` + preload critical fonts.

```html
<!-- In index.html <head> — preload only the font weights you use above the fold -->
<link
  rel="preload"
  href="/fonts/Inter-Variable.woff2"
  as="font"
  type="font/woff2"
  crossorigin
/>

<!-- Load the font stylesheet -->
<link
  rel="stylesheet"
  href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap"
/>
```

For self-hosted fonts (better performance):

```css
/* src/styles/fonts.css */
@font-face {
  font-family: "Inter";
  font-style: normal;
  font-weight: 100 900; /* Variable font range */
  font-display: swap;
  src: url("/fonts/Inter-Variable.woff2") format("woff2");
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+2000-206F;
}

@font-face {
  font-family: "Inter";
  font-style: italic;
  font-weight: 100 900;
  font-display: swap;
  src: url("/fonts/Inter-Variable-Italic.woff2") format("woff2");
  unicode-range: U+0000-00FF;
}
```

**Rules:**

- Self-host for production (no external dependency, better caching)
- Use `woff2` only — supported by all modern browsers
- Load at most 2 font families (1 for headings, 1 for body)
- Preload only the font file used above the fold
- Use `unicode-range` to subset fonts

---

### Phase 2: Responsive Type Scale

Use a modular scale with CSS `clamp()` for fluid typography:

```css
:root {
  /* Base: 16px at 320px viewport → 18px at 1280px */
  --text-base: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);

  /* Scale ratio: ~1.25 (Major Third) */
  --text-sm: clamp(0.875rem, 0.85rem + 0.13vw, 0.9375rem);
  --text-lg: clamp(1.125rem, 1.05rem + 0.38vw, 1.25rem);
  --text-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem);
  --text-2xl: clamp(1.5rem, 1.2rem + 1.5vw, 2rem);
  --text-3xl: clamp(1.875rem, 1.4rem + 2.4vw, 2.5rem);
  --text-4xl: clamp(2.25rem, 1.5rem + 3.75vw, 3.5rem);
  --text-5xl: clamp(3rem, 1.8rem + 6vw, 4.5rem);

  /* Line heights */
  --leading-tight: 1.15;
  --leading-snug: 1.3;
  --leading-normal: 1.6;
  --leading-relaxed: 1.75;

  /* Letter spacing */
  --tracking-tight: -0.02em;
  --tracking-normal: 0;
  --tracking-wide: 0.05em;
}
```

Apply consistently:

```css
h1 {
  font-size: var(--text-5xl);
  line-height: var(--leading-tight);
  letter-spacing: var(--tracking-tight);
  font-weight: 700;
}

h2 {
  font-size: var(--text-3xl);
  line-height: var(--leading-tight);
  letter-spacing: var(--tracking-tight);
  font-weight: 600;
}

h3 {
  font-size: var(--text-2xl);
  line-height: var(--leading-snug);
  font-weight: 600;
}

p,
li,
td {
  font-size: var(--text-base);
  line-height: var(--leading-normal);
}

.text-small {
  font-size: var(--text-sm);
  line-height: var(--leading-normal);
  color: var(--text-secondary);
}
```

---

### Phase 3: Text Rendering

Subtle rendering optimizations that improve readability:

```css
body {
  /* Optimize legibility (activates kerning, ligatures) */
  text-rendering: optimizeLegibility;

  /* Font smoothing for macOS */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  /* Prevent font size inflation on mobile */
  -webkit-text-size-adjust: 100%;
  text-size-adjust: 100%;

  /* Enable font features */
  font-feature-settings:
    "kern" 1,
    /* Kerning */ "liga" 1,
    /* Standard ligatures */ "calt" 1; /* Contextual alternates */

  /* Use tabular numbers in data displays */
  font-variant-numeric: tabular-nums;
}

/* Use proportional numbers in prose */
.prose {
  font-variant-numeric: proportional-nums;
}

/* Tabular numbers for data/stats (aligned columns) */
.data-value,
.stat-number,
.price {
  font-variant-numeric: tabular-nums;
}
```

---

### Phase 4: Typographic Details

Small details that separate amateur from professional:

```css
/* Balanced headings (prevent awkward breaks) */
h1,
h2,
h3,
h4 {
  text-wrap: balance;
}

/* Pretty line breaks for paragraphs */
p {
  text-wrap: pretty;
}

/* Maximum line length for readability (45-75 characters) */
.max-prose {
  max-width: 65ch;
}

/* Proper quote marks */
q {
  quotes: "\201C" "\201D" "\2018" "\2019";
}

/* Hanging punctuation (Safari only, but progressive enhancement) */
.pullquote {
  hanging-punctuation: first last;
}

/* Prevent widows in block text */
p:last-child {
  widows: 2;
  orphans: 2;
}
```

---

### Phase 5: Font Fallback Stack

Define proper fallback stacks to minimize layout shift during font swap:

```css
:root {
  --font-sans:
    "Inter", ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif;

  --font-mono:
    "JetBrains Mono", ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas,
    "Liberation Mono", monospace;

  --font-heading: "Inter", var(--font-sans);
}

body {
  font-family: var(--font-sans);
}

h1,
h2,
h3,
h4,
h5,
h6 {
  font-family: var(--font-heading);
}

code,
pre,
kbd {
  font-family: var(--font-mono);
}
```

**For minimal CLS during font swap**, use the CSS Font Loading API to adjust fallback metrics:

```css
@font-face {
  font-family: "Inter Fallback";
  src: local("Arial");
  ascent-override: 90%;
  descent-override: 22%;
  line-gap-override: 0%;
  size-adjust: 107%;
}
```

---

## Quick Wins Checklist

- [ ] Fonts preloaded in `<head>` with `font-display: swap`
- [ ] Self-hosted fonts instead of Google Fonts CDN
- [ ] Only 2 font families loaded (1 sans, 1 optional mono)
- [ ] Type scale uses `clamp()` for fluid sizing
- [ ] `-webkit-font-smoothing: antialiased` on body
- [ ] `text-wrap: balance` on all headings
- [ ] `max-width: 65ch` on text-heavy containers
- [ ] `font-variant-numeric: tabular-nums` on data/stat displays
- [ ] Minimum 16px font size (prevents iOS input zoom)
- [ ] Proper fallback font stack defined
- [ ] No CLS from font loading (test with Lighthouse)
