---
name: puppeteer-pdf-rendering
description: Master server-side PDF generation with Puppeteer (Node.js) or WeasyPrint (Python). Use for complex multi-page reports, pixel-perfect layouts, and batch PDF generation.
---

# Puppeteer PDF Rendering

Server-side PDF generation using Puppeteer for high-fidelity, complex document rendering.

## Use this skill when

- Creating complex multi-page reports (10+ pages)
- Pixel-perfect print layouts required
- Batch processing multiple PDFs
- Reports with complex CSS/charts

## Do not use this skill when

- Simple 1-page exports (use jsPDF)
- No server-side capability available
- Real-time generation not needed

## Instructions

1. Design HTML template for PDF
2. Configure page layout and margins
3. Handle dynamic data injection
4. Optimize for performance

## Core Concepts

### 1. Installation

```bash
npm install puppeteer
```

### 2. Basic PDF Generation

```javascript
const puppeteer = require("puppeteer");

async function generatePDF(htmlContent, outputPath) {
  const browser = await puppeteer.launch({ headless: "new" });
  const page = await browser.newPage();

  await page.setContent(htmlContent, { waitUntil: "networkidle0" });

  await page.pdf({
    path: outputPath,
    format: "A4",
    printBackground: true,
    margin: {
      top: "20mm",
      right: "15mm",
      bottom: "20mm",
      left: "15mm",
    },
  });

  await browser.close();
}
```

### 3. Template-Based Generation

```javascript
const Handlebars = require("handlebars");

const template = `
<!DOCTYPE html>
<html>
<head>
  <style>
    @page { margin: 0; }
    body { font-family: 'Inter', sans-serif; }
    .header { 
      background: #1f3d3d; 
      color: white; 
      padding: 20px;
    }
    .content { padding: 30px; }
    .metric-card {
      display: inline-block;
      width: 30%;
      padding: 15px;
      margin: 10px;
      border: 1px solid #e5e5e5;
    }
    .grade-a { color: #2180a1; }
    .grade-b { color: #999999; }
    .page-break { page-break-after: always; }
  </style>
</head>
<body>
  <div class="header">
    <h1>{{marketName}} Market Profile</h1>
    <p>Generated: {{generatedDate}}</p>
  </div>
  <div class="content">
    <h2>Economic Overview</h2>
    {{#each economicMetrics}}
    <div class="metric-card">
      <h4>{{label}}</h4>
      <p>{{value}}</p>
      <span class="grade-{{grade}}">Grade: {{grade}}</span>
    </div>
    {{/each}}
  </div>
</body>
</html>
`;

async function generateMarketPDF(marketData) {
  const compiled = Handlebars.compile(template);
  const html = compiled(marketData);

  await generatePDF(html, `./output/${marketData.id}.pdf`);
}
```

### 4. Multi-Page with Headers/Footers

```javascript
await page.pdf({
  path: "report.pdf",
  format: "A4",
  printBackground: true,
  displayHeaderFooter: true,
  headerTemplate: `
    <div style="font-size: 10px; width: 100%; text-align: center; color: #999;">
      SIOR Global Markets - {{marketName}}
    </div>
  `,
  footerTemplate: `
    <div style="font-size: 10px; width: 100%; display: flex; justify-content: space-between; padding: 0 20px;">
      <span>© SIOR ${new Date().getFullYear()}</span>
      <span>Page <span class="pageNumber"></span> of <span class="totalPages"></span></span>
    </div>
  `,
  margin: { top: "40mm", bottom: "30mm", left: "15mm", right: "15mm" },
});
```

### 5. Express.js API Endpoint

```javascript
const express = require("express");
const puppeteer = require("puppeteer");

const app = express();
let browser;

// Initialize browser on startup
async function initBrowser() {
  browser = await puppeteer.launch({
    headless: "new",
    args: ["--no-sandbox", "--disable-setuid-sandbox"],
  });
}

app.post("/api/generate-pdf", async (req, res) => {
  try {
    const { marketId, fields } = req.body;
    const marketData = await fetchMarketData(marketId, fields);

    const page = await browser.newPage();
    await page.setContent(renderTemplate(marketData));

    const pdfBuffer = await page.pdf({ format: "A4", printBackground: true });
    await page.close();

    res.set({
      "Content-Type": "application/pdf",
      "Content-Disposition": `attachment; filename="${marketData.name}-profile.pdf"`,
    });
    res.send(pdfBuffer);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

initBrowser().then(() => app.listen(3000));
```

### 6. Performance Optimization

```javascript
// Reuse browser instance
const browserPool = {
  browser: null,
  async get() {
    if (!this.browser) {
      this.browser = await puppeteer.launch({ headless: "new" });
    }
    return this.browser;
  },
};

// Batch processing
async function generateBatchPDFs(markets) {
  const browser = await browserPool.get();

  for (const market of markets) {
    const page = await browser.newPage();
    await page.setContent(renderTemplate(market));
    await page.pdf({ path: `./output/${market.id}.pdf`, format: "A4" });
    await page.close();
  }
}
```

## Serverless Deployment (AWS Lambda)

```javascript
const chromium = require("@sparticuz/chromium");
const puppeteer = require("puppeteer-core");

exports.handler = async (event) => {
  const browser = await puppeteer.launch({
    args: chromium.args,
    defaultViewport: chromium.defaultViewport,
    executablePath: await chromium.executablePath(),
    headless: chromium.headless,
  });

  const page = await browser.newPage();
  await page.setContent(event.html);

  const pdf = await page.pdf({ format: "A4" });
  await browser.close();

  return {
    statusCode: 200,
    headers: { "Content-Type": "application/pdf" },
    body: pdf.toString("base64"),
    isBase64Encoded: true,
  };
};
```

## Best Practices

### Do's

- **Reuse browser instances** for performance
- **Set timeouts** for page operations
- **Use print media queries** in CSS
- **Close pages** after use to prevent memory leaks

### Don'ts

- **Don't launch new browser per request**
- **Don't use relative paths** for assets
- **Don't skip waitUntil** for dynamic content

## Resources

- [Puppeteer Documentation](https://pptr.dev/)
- [Chromium for AWS Lambda](https://github.com/Sparticuz/chromium)
