---
name: commercial-real-estate-expert
description: Master commercial real estate (CRE) terminology, metrics, and market analysis. Use when building CRE applications, analyzing market data, or creating investment tools.
---

# Commercial Real Estate Expert

Domain expertise in commercial real estate terminology, metrics, and market analysis.

## Use this skill when

- Building commercial real estate applications
- Analyzing CRE market data
- Creating investment analysis tools
- Designing property valuation interfaces
- Understanding SIOR Global Markets data fields

## Do not use this skill when

- Working on residential real estate
- Non-property related projects

## Instructions

1. Apply correct CRE terminology
2. Calculate and display metrics properly
3. Use industry-standard units and formats
4. Validate data against market norms

## Key Property Types

### 1. Office

**Classes:**

- **Class A:** Premium buildings, prime locations, newest/renovated, top rents
- **Class B:** Good quality, competitive rents, well-maintained
- **Class C:** Older buildings, below-average rents, may need renovation

**Key Metrics:**

- Inventory (Total SF)
- Vacancy Rate (%)
- Asking Rent ($/SF/year or $/SF/month)
- Net Absorption (SF)
- Cap Rate (%)

### 2. Industrial

**Subtypes:**

- **Warehouse/Distribution:** Large footprint, high ceilings, dock doors
- **Manufacturing:** Specialized facilities, heavy power
- **Flex/R&D:** Mixed office/warehouse, tech tenants
- **Cold Storage:** Temperature-controlled logistics

**Key Metrics:**

- Clear Height (feet)
- Loading Doors
- Rent ($/SF/year NNN)
- Last Mile Premium

### 3. Retail

**Subtypes:**

- **Regional Mall:** 400,000+ SF, anchored by department stores
- **Power Center:** Big-box anchored, 250,000-600,000 SF
- **Neighborhood Center:** Grocery-anchored, 30,000-150,000 SF
- **Strip Center:** Small retail, 10,000-30,000 SF

**Key Metrics:**

- Sales PSF
- Occupancy Cost Ratio
- Traffic Counts

### 4. Multifamily

**Classes:**

- **Class A:** Luxury, new construction, premium amenities
- **Class B:** Older but well-maintained, moderate rents
- **Class C:** Workforce housing, basic amenities

**Key Metrics:**

- Units
- Rent PSF or $/unit
- Occupancy Rate
- Cap Rate

## Essential CRE Metrics

### Valuation Metrics

```javascript
const cre = {
  // Cap Rate = NOI / Property Value
  capRate: (noi, value) => (noi / value) * 100,

  // Value = NOI / Cap Rate
  propertyValue: (noi, capRate) => noi / (capRate / 100),

  // Net Operating Income = Revenue - Operating Expenses
  noi: (revenue, opex) => revenue - opex,

  // Cash-on-Cash Return = Annual Cash Flow / Total Cash Invested
  cashOnCash: (cashFlow, invested) => (cashFlow / invested) * 100,

  // Debt Service Coverage Ratio = NOI / Annual Debt Service
  dscr: (noi, debtService) => noi / debtService,

  // Loan-to-Value = Loan Amount / Property Value
  ltv: (loan, value) => (loan / value) * 100,
};
```

### Market Metrics

```javascript
const marketMetrics = {
  // Vacancy Rate = Vacant SF / Total Inventory SF
  vacancyRate: (vacant, total) => (vacant / total) * 100,

  // Occupancy Rate = 100 - Vacancy Rate
  occupancyRate: (vacancyRate) => 100 - vacancyRate,

  // Net Absorption = SF Moved In - SF Moved Out
  netAbsorption: (movedIn, movedOut) => movedIn - movedOut,

  // Rent Growth = (Current Rent - Previous Rent) / Previous Rent
  rentGrowth: (current, previous) => ((current - previous) / previous) * 100,

  // Months of Supply = Available Inventory / Monthly Absorption
  monthsOfSupply: (available, monthlyAbsorption) =>
    monthlyAbsorption > 0 ? available / monthlyAbsorption : Infinity,
};
```

## Grade Calculation Logic

For SIOR Global Markets, grades are assigned based on percentile rankings:

```javascript
function calculateGrade(value, metric, benchmarks) {
  const { excellent, good, average, poor } = benchmarks[metric];

  // For metrics where HIGHER is better (GDP, population)
  if (metric.higherIsBetter) {
    if (value >= excellent) return "A";
    if (value >= good) return "B";
    if (value >= average) return "C";
    return "D";
  }

  // For metrics where LOWER is better (vacancy, unemployment)
  if (value <= excellent) return "A";
  if (value <= good) return "B";
  if (value <= average) return "C";
  return "D";
}

// Example benchmarks
const benchmarks = {
  vacancyRate: {
    excellent: 5, // <= 5% = Grade A
    good: 10, // <= 10% = Grade B
    average: 15, // <= 15% = Grade C
    poor: 20, // > 15% = Grade D
    higherIsBetter: false,
  },
  gdpGrowth: {
    excellent: 4, // >= 4% = Grade A
    good: 2.5, // >= 2.5% = Grade B
    average: 1, // >= 1% = Grade C
    poor: 0, // < 1% = Grade D
    higherIsBetter: true,
  },
};
```

## Data Categories (SIOR 101 Fields)

### 1. Identity (8 fields)

Market name, country, region, ID, location data

### 2. Economic (10 fields)

Population, GDP, GDP growth, unemployment, inflation, currency, exchange rate, ease of doing business

### 3. Office (15 fields)

Inventory, vacancy, absorption, rent, rent growth, new supply, cap rate, by class breakdown

### 4. Industrial (15 fields)

Warehouse inventory, vacancy, rent, logistics performance, port proximity, e-commerce penetration

### 5. Retail (12 fields)

Mall inventory, high street vacancy, retail sales, e-commerce share

### 6. Multifamily (10 fields)

Housing units, rent levels, occupancy, price growth

### 7. Infrastructure (8 fields)

Airport passengers, port TEUs, rail connectivity, broadband

### 8. Investment (12 fields)

Transaction volume, foreign investment %, yield trends, REIT presence

### 9. Sustainability (8 fields)

Green building certification, carbon goals, ESG scores

### 10. Custom (3 fields)

Local market specialties, emerging sectors

## Formatting Standards

### Currency

```javascript
// Always specify currency and use local format
const formatCurrency = (value, currency = "USD") => {
  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency,
    minimumFractionDigits: 0,
    maximumFractionDigits: 0,
  }).format(value);
};
```

### Area

```javascript
// US markets: Square Feet (SF)
// International: Square Meters (SM)
const formatArea = (value, unit = "sf") => {
  const formatted = value.toLocaleString();
  return unit === "sf" ? `${formatted} SF` : `${formatted} SM`;
};
```

### Percentages

```javascript
// Always show 1 decimal place for rates
const formatPercent = (value) => `${value.toFixed(1)}%`;
```

## Best Practices

### Do's

- Use industry-standard abbreviations (SF, PSF, NNN, NOI)
- Display rent quotes consistently ($/SF/yr vs $/SF/mo)
- Include data vintage/date
- Note data sources

### Don'ts

- Don't mix imperial and metric without conversion
- Don't display raw values without context
- Don't compare markets without normalization
- Don't omit currency specification

## Common Abbreviations

| Abbr | Meaning                                                |
| ---- | ------------------------------------------------------ |
| SF   | Square Feet                                            |
| SM   | Square Meters                                          |
| PSF  | Per Square Foot                                        |
| NNN  | Triple Net (tenant pays taxes, insurance, maintenance) |
| NOI  | Net Operating Income                                   |
| CAP  | Capitalization Rate                                    |
| DSCR | Debt Service Coverage Ratio                            |
| LTV  | Loan to Value                                          |
| GLA  | Gross Leasable Area                                    |
| NRA  | Net Rentable Area                                      |
| FAR  | Floor Area Ratio                                       |
| TEU  | Twenty-foot Equivalent Unit (shipping)                 |

## Resources

- SIOR Organization: https://www.sior.com/
- CBRE Research: https://www.cbre.com/insights
- JLL Research: https://www.jll.com/research
- CoStar: https://www.costar.com/
