---
name: csv-json-data-pipeline
description: Transform CSV data to JSON for web applications. Use when processing spreadsheet data, converting market data files, or building data ingestion pipelines.
---

# CSV to JSON Data Pipeline

Transform CSV data into structured JSON for web application consumption.

## Use this skill when

- Converting spreadsheet exports to JSON
- Processing quarterly market data updates
- Building data ingestion pipelines
- Transforming bulk data files

## Do not use this skill when

- Real-time data streaming
- Database-to-database ETL
- Complex data transformations (use Python/pandas)

## Instructions

1. Analyze CSV structure and headers
2. Define JSON schema mapping
3. Implement parsing with validation
4. Test with edge cases (empty cells, special chars)

## Core Patterns

### 1. Node.js CSV to JSON

```javascript
const fs = require("fs");
const csv = require("csv-parse/sync");

function csvToJson(csvPath, outputPath) {
  const csvContent = fs.readFileSync(csvPath, "utf-8");

  const records = csv.parse(csvContent, {
    columns: true, // Use first row as headers
    skip_empty_lines: true,
    trim: true,
    cast: true, // Auto-cast numbers
  });

  fs.writeFileSync(outputPath, JSON.stringify(records, null, 2));
  console.log(`Converted ${records.length} records to ${outputPath}`);
  return records;
}
```

### 2. Browser-Based (No Dependencies)

```javascript
function parseCSV(csvText) {
  const lines = csvText.split("\n").filter((line) => line.trim());
  const headers = lines[0].split(",").map((h) => h.trim().replace(/"/g, ""));

  return lines.slice(1).map((line) => {
    const values = parseCSVLine(line);
    const obj = {};
    headers.forEach((header, i) => {
      obj[header] = parseValue(values[i]);
    });
    return obj;
  });
}

function parseCSVLine(line) {
  const result = [];
  let current = "";
  let inQuotes = false;

  for (const char of line) {
    if (char === '"') {
      inQuotes = !inQuotes;
    } else if (char === "," && !inQuotes) {
      result.push(current.trim());
      current = "";
    } else {
      current += char;
    }
  }
  result.push(current.trim());
  return result;
}

function parseValue(val) {
  if (val === "" || val === undefined) return null;
  val = val.replace(/^"|"$/g, "");
  if (!isNaN(val) && val !== "") return Number(val);
  if (val.toLowerCase() === "true") return true;
  if (val.toLowerCase() === "false") return false;
  return val;
}
```

### 3. SIOR Market Data Transformation

```javascript
const fieldMapping = {
  // Identity
  "Market Name": "name",
  Country: "country",
  Region: "region",
  Latitude: "lat",
  Longitude: "lng",

  // Economic
  Population: "population",
  "GDP (Billion USD)": "gdp",
  "GDP Growth (%)": "gdpGrowth",
  "Unemployment Rate (%)": "unemployment",

  // Office
  "Office Inventory (SF)": "officeInventory",
  "Office Vacancy Rate (%)": "officeVacancy",
  "Office Asking Rent ($/SF)": "officeRent",
  "Office Absorption (SF)": "officeAbsorption",

  // Grades
  "Population Grade": "popGrade",
  "GDP Grade": "gdpGrade",
  "Office Vacancy Grade": "officeVacancyGrade",
};

function transformSIORData(csvRecords) {
  return csvRecords.map((record) => {
    const transformed = {
      id: generateId(record["Market Name"], record["Country"]),
    };

    for (const [csvField, jsonField] of Object.entries(fieldMapping)) {
      if (record[csvField] !== undefined) {
        transformed[jsonField] = record[csvField];
      }
    }

    return transformed;
  });
}

function generateId(name, country) {
  return `${name.toLowerCase().replace(/\s+/g, "-")}-${country.toLowerCase().replace(/\s+/g, "-")}`;
}
```

### 4. Validation Layer

```javascript
const validators = {
  required: (value, field) => {
    if (value === null || value === undefined || value === "") {
      return `${field} is required`;
    }
    return null;
  },

  number: (value, field) => {
    if (value !== null && typeof value !== "number") {
      return `${field} must be a number`;
    }
    return null;
  },

  range: (min, max) => (value, field) => {
    if (value < min || value > max) {
      return `${field} must be between ${min} and ${max}`;
    }
    return null;
  },

  enum: (allowed) => (value, field) => {
    if (!allowed.includes(value)) {
      return `${field} must be one of: ${allowed.join(", ")}`;
    }
    return null;
  },

  coordinates: (value, field) => {
    if (field === "lat" && (value < -90 || value > 90)) {
      return "Latitude must be between -90 and 90";
    }
    if (field === "lng" && (value < -180 || value > 180)) {
      return "Longitude must be between -180 and 180";
    }
    return null;
  },
};

const marketSchema = {
  name: [validators.required],
  country: [validators.required],
  region: [validators.required, validators.enum(["APAC", "EMEA", "Americas"])],
  lat: [validators.required, validators.number, validators.coordinates],
  lng: [validators.required, validators.number, validators.coordinates],
  population: [validators.number, validators.range(0, 50000000)],
  gdp: [validators.number, validators.range(0, 5000)],
  officeVacancy: [validators.number, validators.range(0, 100)],
};

function validate(record, schema) {
  const errors = [];

  for (const [field, validatorList] of Object.entries(schema)) {
    for (const validator of validatorList) {
      const error = validator(record[field], field);
      if (error) errors.push({ field, error, value: record[field] });
    }
  }

  return errors;
}
```

### 5. Complete Pipeline Script

```javascript
#!/usr/bin/env node
const fs = require("fs");
const path = require("path");
const csv = require("csv-parse/sync");

const INPUT_DIR = "./data/csv";
const OUTPUT_DIR = "./data/json";

async function runPipeline() {
  console.log("Starting SIOR data pipeline...\n");

  // Read all CSV files
  const csvFiles = fs.readdirSync(INPUT_DIR).filter((f) => f.endsWith(".csv"));

  let allMarkets = [];
  let errors = [];

  for (const file of csvFiles) {
    console.log(`Processing ${file}...`);

    const content = fs.readFileSync(path.join(INPUT_DIR, file), "utf-8");
    const records = csv.parse(content, {
      columns: true,
      trim: true,
      cast: true,
    });

    const transformed = transformSIORData(records);

    // Validate each record
    for (const record of transformed) {
      const recordErrors = validate(record, marketSchema);
      if (recordErrors.length > 0) {
        errors.push({ file, market: record.name, errors: recordErrors });
      } else {
        allMarkets.push(record);
      }
    }
  }

  // Write output
  fs.writeFileSync(
    path.join(OUTPUT_DIR, "markets.json"),
    JSON.stringify(allMarkets, null, 2),
  );

  // Write errors report
  if (errors.length > 0) {
    fs.writeFileSync(
      path.join(OUTPUT_DIR, "errors.json"),
      JSON.stringify(errors, null, 2),
    );
    console.log(`\n⚠️  ${errors.length} validation errors - see errors.json`);
  }

  console.log(`\n✅ Processed ${allMarkets.length} markets to markets.json`);
}

runPipeline().catch(console.error);
```

## Best Practices

### Do's

- Validate data after transformation
- Handle encoding (UTF-8 BOM)
- Log processing stats
- Keep original CSV as backup

### Don'ts

- Don't assume CSV structure is consistent
- Don't skip empty value handling
- Don't trust numeric formats (1,000 vs 1000)

## Resources

- [csv-parse](https://csv.js.org/parse/)
- [Papa Parse](https://www.papaparse.com/) (browser)
