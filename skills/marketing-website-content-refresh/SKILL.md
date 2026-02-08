---
name: marketing-website-content-refresh
description: Quarterly content freshness audit and data update workflow for marketing websites. Detects stale statistics, outdated references, broken links, and content that needs updating. Use for data-driven websites that display market statistics, case studies, or time-sensitive information.
---

# Marketing Website Content Refresh

Systematic content freshness audit for marketing websites with hardcoded or semi-static data.

## Use this skill when

- Running a quarterly content freshness audit
- Market data, statistics, or case studies need updating
- Detecting stale content across a website
- Preparing for a content refresh sprint
- Onboarding a new content manager to the audit process

## Do not use this skill when

- Content is fully CMS-driven with automatic updates
- You need to write new content from scratch (use `content-marketer` or `seo-content-writer`)
- You need SEO strategy rather than content freshness (use `seo-content-auditor`)

## Instructions

### Phase 1: Scan for Staleness

1. Search the codebase for time-sensitive patterns:

```bash
# Find hardcoded years
rg -n '202[0-9]' src/pages/ --include='*.tsx' --include='*.ts'

# Find quarter references
rg -n 'Q[1-4]\s*20' src/pages/

# Find percentage values (likely market data)
rg -n '\d+\.\d+%' src/pages/

# Find dollar amounts
rg -n '\$[\d,]+' src/pages/

# Find "as of" or "updated" references
rg -ni 'as of|updated|latest|current' src/pages/
```

2. For each match, assess staleness:

| Category               | Refresh Cadence | Priority |
| ---------------------- | --------------- | -------- |
| Market vacancy rates   | Quarterly       | High     |
| Rental rates / pricing | Quarterly       | High     |
| GDP / economic data    | Semi-annually   | Medium   |
| Case studies           | Annually        | Medium   |
| Team bios / photos     | Annually        | Low      |
| Company description    | As needed       | Low      |
| Legal disclaimers      | Annually        | High     |

### Phase 2: Source Fresh Data

3. For Australian CRE market data, consult:

| Source                        | Data Type                   | URL                              |
| ----------------------------- | --------------------------- | -------------------------------- |
| CBRE Research                 | Office, industrial, retail  | cbre.com.au/research             |
| JLL Research                  | All sectors                 | jll.com.au/research              |
| Knight Frank                  | Premium office, residential | knightfrank.com.au/research      |
| Savills                       | Industrial, office          | savills.com.au/research-and-news |
| Property Council of Australia | Office Market Report        | propertycouncil.com.au           |
| ABS                           | CPI, GDP, employment        | abs.gov.au                       |
| RBA                           | Interest rates, forecasts   | rba.gov.au                       |
| CoreLogic                     | Property values, trends     | corelogic.com.au                 |

4. Cross-reference multiple sources for key statistics. Discrepancies > 10% should be flagged for review.

### Phase 3: Update Content

5. For each stale data point:
   - Update the value
   - Update the source citation (report name, date)
   - Update the "as of" date if displayed
   - Check that surrounding narrative still makes sense
   - Verify data formatting consistency (e.g., "5.2%" not "5.2 percent")

6. Check for broken links:

```bash
# Install and run linkinator or similar
npx linkinator http://localhost:4173 --skip mailto --recurse
```

### Phase 4: Verify

7. Run build to catch TypeScript errors:

```bash
npm run typecheck
npm run build
```

8. Visually verify updated pages render correctly
9. Check that charts/visualizations reflect new data
10. Update the sitemap `lastmod` dates

### Phase 5: Document

11. Log what was updated:
    - File(s) changed
    - Data sources used
    - Date of update
    - Next scheduled refresh

## Safety

- Always cite data sources — CRE clients verify numbers
- Keep a changelog of data updates for audit trails
- Do not publish data that isn't publicly available or properly licensed
- Mark any projected/forecast data clearly as estimates
