---
name: sior-brand-guidelines
description: Apply SIOR brand guidelines including colors, typography, logos, and visual identity. Use when designing SIOR-branded interfaces, documents, or marketing materials.
---

# SIOR Brand Guidelines

Official SIOR (Society of Industrial and Office Realtors) brand guidelines for consistent visual identity.

## Use this skill when

- Designing SIOR-branded interfaces
- Creating market reports with SIOR branding
- Building components for SIOR website/module
- Generating PDF exports with proper branding

## Do not use this skill when

- Building non-SIOR branded products
- Creating competitor materials

## Instructions

1. Apply official SIOR color palette
2. Use approved typography
3. Follow logo usage guidelines
4. Maintain brand consistency across all touchpoints

## Color Palette

### Primary Colors

```css
:root {
  /* Primary Brand Colors */
  --sior-navy: #1f3d3d; /* Primary - headers, strong text */
  --sior-gold: #b8860b; /* Accent - highlights, CTAs, premium */
  --sior-teal: #2180a1; /* Secondary - links, Grade A */

  /* Neutral Colors */
  --sior-gray-light: #f5f5f5; /* Backgrounds, cards */
  --sior-gray-medium: #999999; /* Secondary text, Grade B */
  --sior-gray-dark: #333333; /* Body text */
  --sior-white: #ffffff; /* White backgrounds */

  /* Functional Colors */
  --sior-success: #28a745; /* Positive indicators */
  --sior-warning: #ffc107; /* Caution/moderate */
  --sior-danger: #dc3545; /* Negative indicators */
}
```

### Grade Indicators

```css
.grade-a {
  color: #2180a1;
} /* Teal - Excellent/Top tier */
.grade-b {
  color: #999999;
} /* Gray - Average/Standard */
.grade-c {
  color: #b8860b;
} /* Gold - Below average (attention) */
.grade-d {
  color: #dc3545;
} /* Red - Poor/Critical */
```

## Typography

### Font Stack

```css
:root {
  /* Primary Font - Headers and Display */
  --font-display: "Montserrat", "Helvetica Neue", Arial, sans-serif;

  /* Secondary Font - Body Text */
  --font-body: "Open Sans", "Segoe UI", Roboto, sans-serif;

  /* Monospace - Data/Code */
  --font-mono: "Roboto Mono", "Courier New", monospace;
}

/* Typography Scale */
h1 {
  font-family: var(--font-display);
  font-size: 2.5rem;
  font-weight: 700;
}
h2 {
  font-family: var(--font-display);
  font-size: 2rem;
  font-weight: 600;
}
h3 {
  font-family: var(--font-display);
  font-size: 1.5rem;
  font-weight: 600;
}
h4 {
  font-family: var(--font-display);
  font-size: 1.25rem;
  font-weight: 500;
}
body {
  font-family: var(--font-body);
  font-size: 1rem;
  font-weight: 400;
}
```

### Google Fonts Import

```html
<link
  href="https://fonts.googleapis.com/css2?family=Montserrat:wght@500;600;700&family=Open+Sans:wght@400;500;600&display=swap"
  rel="stylesheet"
/>
```

## Logo Usage

### Logo Variations

1. **Primary Logo** - Full color on white/light backgrounds
2. **Reversed Logo** - White on dark/navy backgrounds
3. **Gold Accent** - Navy with gold accents for premium materials

### Clear Space

Minimum clear space around logo = height of the "S" in SIOR

### Minimum Sizes

- **Print:** 1 inch / 25mm minimum width
- **Digital:** 100px minimum width

### Don'ts

- Do not stretch or distort
- Do not change colors outside approved palette
- Do not add effects (shadows, glows, gradients)
- Do not place on busy backgrounds

## Component Styles

### Buttons

```css
.btn-primary {
  background: var(--sior-navy);
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 4px;
  font-family: var(--font-display);
  font-weight: 500;
  transition: background 0.2s;
}

.btn-primary:hover {
  background: #2c5454;
}

.btn-secondary {
  background: transparent;
  color: var(--sior-navy);
  border: 2px solid var(--sior-navy);
  padding: 10px 22px;
  border-radius: 4px;
}

.btn-cta {
  background: var(--sior-gold);
  color: white;
  border: none;
  padding: 14px 28px;
  border-radius: 4px;
  font-weight: 600;
}
```

### Cards

```css
.card {
  background: white;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  padding: 24px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.card-header {
  border-bottom: 1px solid #e5e5e5;
  padding-bottom: 16px;
  margin-bottom: 16px;
}

.card-premium {
  border-color: var(--sior-gold);
  border-width: 2px;
}
```

### Tables

```css
.sior-table {
  width: 100%;
  border-collapse: collapse;
  font-family: var(--font-body);
}

.sior-table th {
  background: var(--sior-navy);
  color: white;
  padding: 12px 16px;
  text-align: left;
  font-weight: 600;
}

.sior-table td {
  padding: 12px 16px;
  border-bottom: 1px solid #e5e5e5;
}

.sior-table tr:nth-child(even) {
  background: var(--sior-gray-light);
}
```

## PDF Template Styles

```css
@media print {
  .pdf-header {
    background: var(--sior-navy);
    color: white;
    padding: 40px;
  }

  .pdf-header h1 {
    font-size: 28px;
    margin: 0;
  }

  .pdf-footer {
    position: fixed;
    bottom: 0;
    width: 100%;
    padding: 20px 40px;
    background: var(--sior-gray-light);
    font-size: 10px;
    color: var(--sior-gray-medium);
  }

  .pdf-grade-badge {
    display: inline-block;
    padding: 4px 12px;
    border-radius: 4px;
    font-weight: 600;
  }

  .pdf-grade-a {
    background: #e3f2fd;
    color: #2180a1;
  }
  .pdf-grade-b {
    background: #f5f5f5;
    color: #666666;
  }
  .pdf-grade-c {
    background: #fff8e1;
    color: #b8860b;
  }
}
```

## Globe Visualization Theming

```javascript
const siorGlobeTheme = {
  pointColors: {
    default: "#2180a1", // Teal
    selected: "#b8860b", // Gold
    hover: "#1f3d3d", // Navy
  },
  atmosphereColor: "#1f3d3d",
  backgroundColor: "#0a0a1a",
  labelStyle: {
    fontFamily: "Montserrat, sans-serif",
    fontSize: "12px",
    color: "#ffffff",
  },
};
```

## Best Practices

### Do's

- Use navy (#1f3d3d) as dominant brand color
- Apply gold (#b8860b) sparingly for emphasis
- Maintain high contrast for accessibility
- Use consistent spacing (8px grid system)

### Don'ts

- Don't use gradients on primary colors
- Don't mix fonts outside the approved stack
- Don't use low-contrast color combinations
- Don't overcrowd layouts

## Resources

- SIOR Brand Kit (available at `/docs/SIOR Branding/`)
- Official SIOR website: https://www.sior.com/
