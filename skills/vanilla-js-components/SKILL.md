---
name: vanilla-js-components
description: Create framework-agnostic web components using Vanilla JavaScript. Use when building portable, CMS-embeddable modules that work without React, Vue, or Angular dependencies.
---

# Vanilla JS Components

Framework-agnostic web components for maximum portability and CMS compatibility.

## Use this skill when

- Building embeddable modules for any CMS
- Creating drop-in components without dependencies
- Need maximum browser compatibility
- WordPress, Drupal, or custom CMS integration

## Do not use this skill when

- Building complex SPAs (use React/Vue)
- Heavy state management required
- Team prefers framework approach

## Instructions

1. Design component API (config, callbacks)
2. Use ES6+ with transpilation fallback
3. Isolate styles with namespacing
4. Test across target browsers

## Core Patterns

### 1. Module Pattern (IIFE)

```javascript
const SIORGlobe = (function () {
  "use strict";

  // Private variables
  let globe = null;
  let config = {};

  // Private methods
  function loadDependencies(callback) {
    const script = document.createElement("script");
    script.src = "https://unpkg.com/globe.gl@2";
    script.onload = callback;
    document.head.appendChild(script);
  }

  function renderGlobe(container, data) {
    globe = Globe()
      .globeImageUrl(config.globeImage)
      .pointsData(data)
      .pointLat("lat")
      .pointLng("lng")(container);
  }

  // Public API
  return {
    init: function (options) {
      config = Object.assign(
        {
          containerId: "sior-globe",
          globeImage: "//unpkg.com/three-globe/example/img/earth-dark.jpg",
          onMarketClick: null,
          apiEndpoint: "/api/markets",
        },
        options,
      );

      loadDependencies(function () {
        fetch(config.apiEndpoint)
          .then((r) => r.json())
          .then((data) => {
            const container = document.getElementById(config.containerId);
            renderGlobe(container, data);
          });
      });
    },

    flyTo: function (lat, lng) {
      if (globe) {
        globe.pointOfView({ lat, lng, altitude: 1.5 }, 1000);
      }
    },

    destroy: function () {
      if (globe) {
        globe._destructor();
        globe = null;
      }
    },
  };
})();

// Usage
SIORGlobe.init({
  containerId: "my-globe",
  onMarketClick: function (market) {
    console.log("Clicked:", market.name);
  },
});
```

### 2. Class-Based Component

```javascript
class MarketCard {
  constructor(container, options = {}) {
    this.container =
      typeof container === "string"
        ? document.querySelector(container)
        : container;

    this.options = {
      showGrade: true,
      currency: "USD",
      ...options,
    };

    this.data = null;
    this.element = null;
  }

  async load(marketId) {
    const response = await fetch(`/api/markets/${marketId}`);
    this.data = await response.json();
    this.render();
    return this;
  }

  render() {
    if (!this.data) return;

    const html = `
      <div class="sior-market-card">
        <div class="sior-card-header">
          <h3>${this.data.name}</h3>
          <span class="sior-region">${this.data.region}</span>
        </div>
        <div class="sior-card-body">
          <div class="sior-metric">
            <span class="sior-label">Population</span>
            <span class="sior-value">${this.formatNumber(this.data.population)}</span>
            ${this.options.showGrade ? `<span class="sior-grade grade-${this.data.popGrade.toLowerCase()}">${this.data.popGrade}</span>` : ""}
          </div>
          <div class="sior-metric">
            <span class="sior-label">Office Vacancy</span>
            <span class="sior-value">${this.data.officeVacancy}%</span>
          </div>
        </div>
      </div>
    `;

    this.element = document.createElement("div");
    this.element.innerHTML = html;
    this.container.appendChild(this.element.firstElementChild);
  }

  formatNumber(num) {
    if (num >= 1000000) return (num / 1000000).toFixed(1) + "M";
    if (num >= 1000) return (num / 1000).toFixed(1) + "K";
    return num.toString();
  }

  destroy() {
    if (this.element && this.element.parentNode) {
      this.element.parentNode.removeChild(this.element);
    }
  }
}

// Usage
const card = new MarketCard("#market-container");
card.load("sydney-australia");
```

### 3. Web Components (Custom Elements)

```javascript
class SIORMarketBadge extends HTMLElement {
  static get observedAttributes() {
    return ["market-id", "show-grade"];
  }

  constructor() {
    super();
    this.attachShadow({ mode: "open" });
  }

  connectedCallback() {
    this.render();
    this.loadData();
  }

  attributeChangedCallback(name, oldValue, newValue) {
    if (oldValue !== newValue) {
      this.loadData();
    }
  }

  async loadData() {
    const marketId = this.getAttribute("market-id");
    if (!marketId) return;

    const response = await fetch(`/api/markets/${marketId}`);
    this.data = await response.json();
    this.render();
  }

  render() {
    const showGrade = this.getAttribute("show-grade") !== "false";

    this.shadowRoot.innerHTML = `
      <style>
        :host {
          display: inline-flex;
          align-items: center;
          padding: 8px 12px;
          background: #f5f5f5;
          border-radius: 4px;
          font-family: 'Open Sans', sans-serif;
        }
        .name { font-weight: 600; color: #1f3d3d; }
        .grade {
          margin-left: 8px;
          padding: 2px 8px;
          border-radius: 3px;
          font-size: 12px;
        }
        .grade-a { background: #e3f2fd; color: #2180a1; }
        .grade-b { background: #f5f5f5; color: #666; }
      </style>
      <span class="name">${this.data?.name || "Loading..."}</span>
      ${
        showGrade && this.data?.grade
          ? `<span class="grade grade-${this.data.grade.toLowerCase()}">${this.data.grade}</span>`
          : ""
      }
    `;
  }
}

customElements.define("sior-market-badge", SIORMarketBadge);

// Usage in HTML
// <sior-market-badge market-id="london-uk" show-grade="true"></sior-market-badge>
```

### 4. Event System

```javascript
const EventBus = {
  events: {},

  on(event, callback) {
    if (!this.events[event]) this.events[event] = [];
    this.events[event].push(callback);
    return () => this.off(event, callback);
  },

  off(event, callback) {
    if (!this.events[event]) return;
    this.events[event] = this.events[event].filter((cb) => cb !== callback);
  },

  emit(event, data) {
    if (!this.events[event]) return;
    this.events[event].forEach((callback) => callback(data));
  },
};

// Usage across components
EventBus.on("market:selected", (market) => {
  console.log("Selected:", market.name);
});

EventBus.emit("market:selected", { id: "sydney", name: "Sydney" });
```

### 5. CSS Namespacing

```css
/* Prefix all classes to avoid conflicts */
.sior-module {
  font-family: "Open Sans", sans-serif;
  font-size: 16px;
  line-height: 1.5;
  color: #333;
}

.sior-module * {
  box-sizing: border-box;
}

.sior-module .sior-btn {
  background: #1f3d3d;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
}

.sior-module .sior-card {
  background: white;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  padding: 20px;
}
```

## Embedding Pattern

```html
<!-- Single script embed -->
<div id="sior-global-markets"></div>
<script src="https://cdn.sior.com/global-markets.min.js"></script>
<script>
  SIORGlobalMarkets.init({
    container: "#sior-global-markets",
    apiKey: "YOUR_API_KEY",
    theme: "light",
  });
</script>
```

## Best Practices

### Do's

- Use IIFE or ES6 modules to avoid global pollution
- Namespace all CSS classes
- Support both ID and element reference for containers
- Provide destroy/cleanup methods
- Use async/await with fallbacks

### Don'ts

- Don't pollute global namespace
- Don't assume jQuery or other libraries exist
- Don't use CSS that leaks outside component
- Don't block main thread with sync operations

## Browser Compatibility

```javascript
// Feature detection
const supportsModules = "noModule" in HTMLScriptElement.prototype;
const supportsCustomElements = "customElements" in window;
const supportsFetch = "fetch" in window;

// Polyfill loader
if (!supportsFetch) {
  loadScript("https://cdn.jsdelivr.net/npm/whatwg-fetch@3");
}
```

## Resources

- [Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components)
- [ES6 Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
