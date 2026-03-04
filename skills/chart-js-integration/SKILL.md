---
name: chart-js-integration
description: Master Chart.js for data visualization with responsive, animated charts. Use when creating dashboards, analytics displays, or data-driven interfaces. Covers bar, line, pie, doughnut, and mixed chart types.
---

# Chart.js Integration

Lightweight, responsive data visualization using Chart.js.

## Use this skill when

- Creating dashboard visualizations
- Building analytics displays
- Rendering time-series data
- Displaying comparative metrics

## Do not use this skill when

- Need complex statistical charts (use D3.js)
- Building real-time streaming charts (use Plotly)
- Creating maps or geo visualizations

## Instructions

1. Clarify chart type and data format
2. Design with accessibility in mind
3. Implement responsive behavior
4. Validate across screen sizes

## Core Concepts

### 1. Installation

```html
<!-- CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4"></script>

<!-- npm -->
npm install chart.js
```

### 2. Basic Chart Setup

```javascript
const ctx = document.getElementById("myChart").getContext("2d");
const chart = new Chart(ctx, {
  type: "bar",
  data: {
    labels: ["Jan", "Feb", "Mar", "Apr", "May"],
    datasets: [
      {
        label: "Revenue ($M)",
        data: [12, 19, 15, 25, 22],
        backgroundColor: "#2180a1",
        borderRadius: 4,
      },
    ],
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    plugins: {
      legend: { display: false },
    },
  },
});
```

### 3. Common Chart Types

```javascript
// Line Chart
const lineChart = new Chart(ctx, {
  type: "line",
  data: {
    labels: months,
    datasets: [
      {
        label: "GDP Growth",
        data: [2.1, 2.3, 2.5, 2.4, 2.8],
        borderColor: "#b8860b",
        fill: false,
        tension: 0.4,
      },
    ],
  },
});

// Doughnut Chart
const doughnutChart = new Chart(ctx, {
  type: "doughnut",
  data: {
    labels: ["Office", "Industrial", "Retail", "Multifamily"],
    datasets: [
      {
        data: [35, 28, 22, 15],
        backgroundColor: ["#1f3d3d", "#2180a1", "#b8860b", "#999999"],
      },
    ],
  },
  options: {
    cutout: "60%",
  },
});

// Horizontal Bar Chart
const horizontalBar = new Chart(ctx, {
  type: "bar",
  data: {
    labels: ["New York", "London", "Tokyo", "Sydney", "Singapore"],
    datasets: [
      {
        label: "Vacancy Rate (%)",
        data: [8.5, 6.2, 4.1, 12.3, 5.8],
        backgroundColor: "#2180a1",
      },
    ],
  },
  options: {
    indexAxis: "y",
  },
});
```

### 4. Multiple Datasets (Comparison)

```javascript
const comparisonChart = new Chart(ctx, {
  type: "bar",
  data: {
    labels: ["Q1", "Q2", "Q3", "Q4"],
    datasets: [
      {
        label: "Class A Rent",
        data: [45, 48, 52, 55],
        backgroundColor: "#1f3d3d",
      },
      {
        label: "Class B Rent",
        data: [32, 34, 35, 38],
        backgroundColor: "#2180a1",
      },
    ],
  },
  options: {
    plugins: {
      legend: { position: "top" },
    },
    scales: {
      y: {
        beginAtZero: true,
        title: { display: true, text: "$/sqft" },
      },
    },
  },
});
```

### 5. Custom Tooltips

```javascript
options: {
  plugins: {
    tooltip: {
      backgroundColor: '#1f3d3d',
      titleFont: { size: 14, weight: 'bold' },
      bodyFont: { size: 12 },
      padding: 12,
      callbacks: {
        label: (context) => {
          const value = context.parsed.y;
          return `$${value.toLocaleString()}M`;
        }
      }
    }
  }
}
```

### 6. Responsive Configuration

```javascript
options: {
  responsive: true,
  maintainAspectRatio: false,
  onResize: (chart, size) => {
    // Adjust font size on smaller screens
    const fontSize = size.width < 400 ? 10 : 12;
    chart.options.plugins.legend.labels.font.size = fontSize;
  }
}
```

### 7. Updating Data Dynamically

```javascript
function updateChart(newData) {
  chart.data.datasets[0].data = newData;
  chart.update("active"); // Smooth animation
}

// Replace entire dataset
function replaceDataset(labels, data) {
  chart.data.labels = labels;
  chart.data.datasets[0].data = data;
  chart.update();
}
```

## Real Estate Dashboard Example

```javascript
// Market Overview Charts
function createMarketCharts(marketData) {
  // Rental Trends
  new Chart(document.getElementById("rentalTrends"), {
    type: "line",
    data: {
      labels: marketData.quarters,
      datasets: [
        {
          label: "Office Rent ($/sqft)",
          data: marketData.officeRent,
          borderColor: "#1f3d3d",
          fill: false,
        },
        {
          label: "Industrial Rent ($/sqft)",
          data: marketData.industrialRent,
          borderColor: "#2180a1",
          fill: false,
        },
      ],
    },
    options: {
      scales: {
        y: { beginAtZero: false },
      },
    },
  });

  // Vacancy Comparison
  new Chart(document.getElementById("vacancyChart"), {
    type: "doughnut",
    data: {
      labels: ["Occupied", "Vacant"],
      datasets: [
        {
          data: [100 - marketData.vacancyRate, marketData.vacancyRate],
          backgroundColor: ["#2180a1", "#e5e5e5"],
        },
      ],
    },
    options: {
      cutout: "70%",
      plugins: {
        legend: { display: false },
      },
    },
  });
}
```

## Best Practices

### Do's

- **Set maintainAspectRatio: false** for flexible sizing
- **Use consistent colors** across dashboards
- **Animate updates** with `chart.update('active')`
- **Destroy charts** before recreating: `chart.destroy()`

### Don'ts

- **Don't overload with data** - keep datasets readable
- **Don't skip accessibility** - add aria-labels
- **Don't use 3D effects** - they distort perception

## Resources

- [Chart.js Documentation](https://www.chartjs.org/docs/)
- [Chart.js Samples](https://www.chartjs.org/samples/)
