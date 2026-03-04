---
name: jspdf-generation
description: Master client-side PDF generation with jsPDF and html2canvas. Use when creating downloadable reports, invoices, or document exports directly in the browser without server-side processing.
---

# jsPDF Generation

Client-side PDF generation using jsPDF and html2canvas for browser-based document creation.

## Use this skill when

- Generating downloadable reports in-browser
- Creating simple 1-2 page PDFs
- Exporting dashboard views as PDF
- Building invoice/receipt generators

## Do not use this skill when

- Complex multi-page reports (use Puppeteer)
- Pixel-perfect print layouts required
- Server-side batch PDF generation

## Instructions

1. Clarify PDF structure and content
2. Design for print dimensions (A4/Letter)
3. Implement with proper scaling
4. Test across browsers

## Core Concepts

### 1. Installation

```html
<!-- CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<!-- npm -->
npm install jspdf html2canvas
```

### 2. Basic PDF Creation

```javascript
const { jsPDF } = window.jspdf;

function generatePDF() {
  const doc = new jsPDF({
    orientation: "portrait",
    unit: "mm",
    format: "a4",
  });

  // Add text
  doc.setFontSize(24);
  doc.setTextColor(31, 61, 61); // Navy
  doc.text("Market Profile Report", 20, 30);

  // Add subtitle
  doc.setFontSize(14);
  doc.setTextColor(100);
  doc.text("Sydney, Australia", 20, 40);

  // Add body text
  doc.setFontSize(11);
  doc.setTextColor(50);
  doc.text("Generated on: " + new Date().toLocaleDateString(), 20, 55);

  // Save
  doc.save("market-profile.pdf");
}
```

### 3. HTML to PDF with html2canvas

```javascript
async function exportElementToPDF(elementId, filename) {
  const element = document.getElementById(elementId);

  const canvas = await html2canvas(element, {
    scale: 2, // Higher quality
    useCORS: true, // Allow cross-origin images
    logging: false,
  });

  const imgData = canvas.toDataURL("image/png");
  const pdf = new jsPDF("p", "mm", "a4");

  const pageWidth = pdf.internal.pageSize.getWidth();
  const pageHeight = pdf.internal.pageSize.getHeight();
  const imgWidth = pageWidth - 20; // 10mm margins
  const imgHeight = (canvas.height * imgWidth) / canvas.width;

  pdf.addImage(imgData, "PNG", 10, 10, imgWidth, imgHeight);
  pdf.save(filename);
}
```

### 4. Structured Report Layout

```javascript
function generateMarketReport(marketData) {
  const doc = new jsPDF();
  const pageWidth = doc.internal.pageSize.getWidth();
  let yPos = 20;

  // Header with logo placeholder
  doc.setFillColor(31, 61, 61);
  doc.rect(0, 0, pageWidth, 35, "F");

  doc.setTextColor(255);
  doc.setFontSize(20);
  doc.text("SIOR Global Markets", 20, 22);
  doc.setFontSize(12);
  doc.text(marketData.name + " Market Profile", 20, 30);

  yPos = 50;

  // Section: Economic Overview
  doc.setTextColor(31, 61, 61);
  doc.setFontSize(16);
  doc.text("Economic Overview", 20, yPos);
  yPos += 10;

  doc.setFontSize(11);
  doc.setTextColor(50);

  const metrics = [
    ["Population", marketData.population.toLocaleString()],
    ["GDP", `$${marketData.gdp}B`],
    ["GDP Growth", `${marketData.gdpGrowth}%`],
    ["Unemployment", `${marketData.unemployment}%`],
  ];

  metrics.forEach(([label, value]) => {
    doc.text(`${label}: ${value}`, 25, yPos);
    yPos += 7;
  });

  yPos += 10;

  // Section: Office Market
  doc.setTextColor(31, 61, 61);
  doc.setFontSize(16);
  doc.text("Office Market", 20, yPos);
  yPos += 10;

  // Add a simple table
  doc.autoTable({
    startY: yPos,
    head: [["Metric", "Value", "Grade"]],
    body: [
      ["Inventory (sqft)", marketData.officeInventory, marketData.officeGrade],
      ["Vacancy Rate", `${marketData.officeVacancy}%`, "-"],
      ["Asking Rent", `$${marketData.officeRent}/sqft`, "-"],
    ],
    theme: "grid",
    headStyles: { fillColor: [31, 61, 61] },
  });

  // Footer
  const pageCount = doc.internal.getNumberOfPages();
  doc.setFontSize(8);
  doc.setTextColor(150);
  doc.text(`Page 1 of ${pageCount}`, pageWidth - 30, 287);
  doc.text("© SIOR " + new Date().getFullYear(), 20, 287);

  doc.save(`${marketData.name.toLowerCase().replace(/ /g, "-")}-profile.pdf`);
}
```

### 5. Adding Images

```javascript
// Add image from URL
async function addImageToPDF(doc, imageUrl, x, y, width, height) {
  return new Promise((resolve) => {
    const img = new Image();
    img.crossOrigin = "anonymous";
    img.onload = () => {
      const canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;
      canvas.getContext("2d").drawImage(img, 0, 0);
      const dataUrl = canvas.toDataURL("image/png");
      doc.addImage(dataUrl, "PNG", x, y, width, height);
      resolve();
    };
    img.src = imageUrl;
  });
}

// Usage
await addImageToPDF(doc, "/assets/sior-logo.png", 15, 10, 40, 15);
```

### 6. Multi-Page Documents

```javascript
function generateMultiPageReport(markets) {
  const doc = new jsPDF();

  markets.forEach((market, index) => {
    if (index > 0) {
      doc.addPage();
    }

    // Add content for this market
    doc.setFontSize(18);
    doc.text(market.name, 20, 30);
    // ... more content
  });

  doc.save("multi-market-report.pdf");
}
```

### 7. Using jsPDF-AutoTable Plugin

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.1/jspdf.plugin.autotable.min.js"></script>
```

```javascript
doc.autoTable({
  startY: 60,
  head: [["Field", "Value", "Grade"]],
  body: [
    ["Population", "5.3M", "A"],
    ["GDP", "$400B", "A"],
    ["Office Vacancy", "8.5%", "B"],
    ["Industrial Rent", "$12/sqft", "A"],
  ],
  theme: "striped",
  headStyles: {
    fillColor: [31, 61, 61],
    textColor: 255,
  },
  alternateRowStyles: { fillColor: [245, 245, 245] },
  columnStyles: {
    2: { halign: "center", fontStyle: "bold" },
  },
});
```

## Complete Example

```javascript
async function exportMarketProfile(marketId) {
  const market = await fetchMarket(marketId);
  const doc = new jsPDF();

  // Header
  doc.setFillColor(31, 61, 61);
  doc.rect(0, 0, 210, 40, "F");
  doc.setTextColor(255);
  doc.setFontSize(22);
  doc.text(market.name, 20, 25);
  doc.setFontSize(10);
  doc.text("SIOR Global Market Profile", 20, 35);

  // Content sections
  let y = 55;

  // Key metrics row
  const metrics = [
    { label: "Population", value: market.population, grade: market.popGrade },
    { label: "GDP", value: market.gdp, grade: market.gdpGrade },
    { label: "Vacancy", value: market.vacancy, grade: market.vacancyGrade },
  ];

  doc.setFontSize(10);
  metrics.forEach((m, i) => {
    const x = 20 + i * 60;
    doc.setTextColor(100);
    doc.text(m.label, x, y);
    doc.setTextColor(31, 61, 61);
    doc.setFontSize(14);
    doc.text(m.value, x, y + 8);
    doc.setFontSize(10);
  });

  // Save
  doc.save(`${market.name.toLowerCase()}-profile.pdf`);
}
```

## Best Practices

### Do's

- **Use scale: 2** in html2canvas for print quality
- **Set proper margins** (10-20mm)
- **Test file size** - compress images before including
- **Add page numbers** for multi-page docs

### Don'ts

- **Don't capture hidden elements** - ensure visibility
- **Don't use web fonts** without embedding
- **Don't exceed ~5MB** file size for browser generation

## Resources

- [jsPDF Documentation](https://raw.githack.com/MrRio/jsPDF/master/docs/)
- [jsPDF-AutoTable](https://github.com/simonbengtsson/jsPDF-AutoTable)
- [html2canvas Docs](https://html2canvas.hertzen.com/)
