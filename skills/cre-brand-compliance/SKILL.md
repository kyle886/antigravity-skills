---
name: cre-brand-compliance
description: Audit and enforce brand consistency for commercial real estate companies. Covers visual identity (colors, typography, logos), tone of voice, content guidelines, and cross-platform brand cohesion. Use when launching a new website, rebranding, or auditing existing brand presence.
---

# CRE Brand Compliance

Brand consistency audit and enforcement for commercial real estate companies, ensuring professional presentation across all digital touchpoints.

## Use this skill when

- Launching or relaunching a company website
- Auditing brand consistency across pages and platforms
- Onboarding new design/content contributors
- Preparing for investor or client presentations
- Rebranding or refreshing visual identity
- Auditing social media and marketing collateral

## Do not use this skill when

- Creating brand guidelines from scratch (use a brand strategist instead)
- Designing logos or visual identity systems
- Legal compliance for marketing materials

## Instructions

### Phase 1: Define Brand Standards

1. Collect or document the brand's core identity:

   **Visual Identity**
   - Primary colors (hex/HSL values, dark/light variants)
   - Accent/highlight colors
   - Typography (primary font, heading font, fallback stack)
   - Logo variants (full, icon, horizontal, vertical, monochrome)
   - Minimum logo clear space and sizing rules
   - Photography style (if applicable)

   **Tone of Voice**
   - CRE industry standard: Professional, authoritative, data-driven
   - Brand personality: Is the firm institutional, boutique, innovative?
   - Language conventions: British vs American English (AU firms typically use British)
   - Prohibited language: Jargon to avoid, competitor mentions

   **Content Guidelines**
   - How to describe the company in one line
   - How to describe services
   - How to present market data (sources, attribution)
   - How to handle disclaimers and legal language

### Phase 2: Audit Digital Presence

2. For each page on the website, check:
   - [ ] Logo is correctly rendered and sized
   - [ ] Colors match brand palette (no ad-hoc #hex values)
   - [ ] Typography is consistent (no mixed font families)
   - [ ] Heading hierarchy follows brand guidelines
   - [ ] CTAs use consistent styling and language
   - [ ] Images match photography/illustration style
   - [ ] Footer contains correct company info

3. Cross-platform consistency:
   - [ ] Social media profiles match website branding
   - [ ] Email templates use brand colors and fonts
   - [ ] PDF exports/reports use brand templates
   - [ ] OG images and social cards match brand

### Phase 3: Codify in Code

4. For web applications, enforce brand via:
   - CSS custom properties / design tokens for all brand colors
   - Tailwind CSS theme configuration
   - Component library with branded variants (buttons, cards, headers)
   - Shared constants file for brand copy (company tagline, descriptions)

5. Document brand in the repo:
   - `BRAND.md` or `docs/brand-guidelines.md`
   - Design tokens in `src/styles/tokens.css` or tailwind config
   - Logo assets in `src/assets/` with clear naming

### Phase 4: Report

6. Generate a brand compliance report with:
   - Pages audited
   - Issues found (categorized by severity)
   - Recommendations
   - Before/after screenshots

## CRE-Specific Considerations

- **Data presentation**: CRE firms must cite data sources (CBRE, JLL, Knight Frank). Ensure consistent citation formatting
- **Market maps**: Verify color coding is consistent across all map visualizations
- **Financial figures**: Use consistent formatting (AUD, decimal places, per sqm vs per annum)
- **Legal disclaimers**: CRE marketing often requires disclaimers about forward-looking statements
- **Professional photography**: Avoid stock photos on key pages — CRE clients expect authentic property images

## Safety

- Do not modify logos or brand assets without designer/stakeholder approval
- Keep backup of original assets before any modifications
- Verify color changes maintain WCAG AA contrast ratios
