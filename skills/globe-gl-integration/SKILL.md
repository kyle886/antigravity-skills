---
name: globe-gl-integration
description: Master Globe.gl for interactive 3D globe visualization with WebGL. Use when building geographic data visualizations, market maps, or location-based interfaces. Handles point rendering, tooltips, click interactions, and camera controls.
---

# Globe.gl Integration

Interactive 3D globe visualization using Globe.gl (Three.js wrapper) for displaying geographic data points.

## Use this skill when

- Building interactive 3D globe visualizations
- Displaying geographic data points (markets, locations, offices)
- Creating drill-down map interfaces
- Implementing location-based dashboards

## Do not use this skill when

- Building 2D maps (use Mapbox/Leaflet instead)
- Simple static map images
- Non-geographic data visualization

## Instructions

1. Clarify data format (lat/lng coordinates required)
2. Design point styling and interaction patterns
3. Implement with performance considerations
4. Validate on target devices/browsers

## Core Concepts

### 1. Installation & Setup

```html
<!-- CDN (recommended for Vanilla JS) -->
<script src="https://unpkg.com/globe.gl@2"></script>

<!-- Or npm -->
<script type="module">
  import Globe from "globe.gl";
</script>
```

### 2. Basic Globe Initialization

```javascript
// Create globe instance
const globe = Globe()
  .globeImageUrl("//unpkg.com/three-globe/example/img/earth-blue-marble.jpg")
  .bumpImageUrl("//unpkg.com/three-globe/example/img/earth-topology.png")
  .backgroundImageUrl("//unpkg.com/three-globe/example/img/night-sky.png")(
  document.getElementById("globe-container"),
);

// Set initial camera position
globe.pointOfView({ lat: 30, lng: 0, altitude: 2.5 });
```

### 3. Adding Data Points

```javascript
const marketsData = [
  { lat: 40.7128, lng: -74.006, name: "New York", size: 0.5, color: "#b8860b" },
  { lat: 51.5074, lng: -0.1278, name: "London", size: 0.4, color: "#2180a1" },
  {
    lat: -33.8688,
    lng: 151.2093,
    name: "Sydney",
    size: 0.35,
    color: "#1f3d3d",
  },
];

globe
  .pointsData(marketsData)
  .pointLat("lat")
  .pointLng("lng")
  .pointAltitude(0.01)
  .pointRadius("size")
  .pointColor("color")
  .pointLabel((d) => `<b>${d.name}</b>`)
  .onPointClick((point, event) => {
    console.log("Clicked:", point.name);
    showMarketDetail(point);
  })
  .onPointHover((point, prevPoint) => {
    document.body.style.cursor = point ? "pointer" : "default";
  });
```

### 4. Custom Point Rendering

```javascript
// HTML Labels for rich tooltips
globe
  .htmlElementsData(marketsData)
  .htmlLat("lat")
  .htmlLng("lng")
  .htmlAltitude(0.02)
  .htmlElement((d) => {
    const el = document.createElement("div");
    el.className = "market-label";
    el.innerHTML = `
      <div class="market-dot" style="background: ${d.color}"></div>
      <span class="market-name">${d.name}</span>
    `;
    el.style.cssText = "pointer-events: auto; cursor: pointer;";
    el.onclick = () => showMarketDetail(d);
    return el;
  });
```

### 5. Camera Controls

```javascript
// Fly to location
function flyToMarket(lat, lng) {
  globe.pointOfView({ lat, lng, altitude: 1.5 }, 1000); // 1 second transition
}

// Auto-rotate
globe.controls().autoRotate = true;
globe.controls().autoRotateSpeed = 0.5;

// Disable zoom limits
globe.controls().minDistance = 101; // Close limit
globe.controls().maxDistance = 500; // Far limit
```

### 6. Performance Optimization

```javascript
// For large datasets (1000+ points)
globe
  .pointsMerge(true) // Merge points into single mesh
  .pointResolution(6); // Lower polygon count

// Render quality based on device
const isMobile = /iPhone|iPad|iPod|Android/i.test(navigator.userAgent);
if (isMobile) {
  globe.rendererConfig({ antialias: false }).pointResolution(4);
}
```

### 7. Responsive Sizing

```javascript
function resizeGlobe() {
  const container = document.getElementById("globe-container");
  globe.width(container.clientWidth).height(container.clientHeight);
}

window.addEventListener("resize", resizeGlobe);
resizeGlobe();
```

## Complete Example

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      #globe-container {
        width: 100%;
        height: 600px;
        background: #0a0a1a;
      }
      .market-label {
        display: flex;
        align-items: center;
        font-family: "Inter", sans-serif;
        font-size: 12px;
        color: white;
      }
      .market-dot {
        width: 8px;
        height: 8px;
        border-radius: 50%;
        margin-right: 4px;
      }
    </style>
  </head>
  <body>
    <div id="globe-container"></div>
    <script src="https://unpkg.com/globe.gl@2"></script>
    <script>
      const markets = [
        /* your data */
      ];

      const globe = Globe()
        .globeImageUrl("//unpkg.com/three-globe/example/img/earth-dark.jpg")
        .pointsData(markets)
        .pointLat("lat")
        .pointLng("lng")
        .pointRadius((d) => d.size * 0.5)
        .pointColor("color")
        .pointLabel((d) => d.name)
        .onPointClick((point) => {
          window.location.hash = `market/${point.id}`;
        })(document.getElementById("globe-container"));

      globe.pointOfView({ lat: 20, lng: 0, altitude: 2.2 });
      globe.controls().autoRotate = true;
    </script>
  </body>
</html>
```

## Best Practices

### Do's

- **Preload textures** for faster initial render
- **Use pointsMerge** for datasets > 500 points
- **Provide fallback** for WebGL-unsupported browsers
- **Test on mobile** - performance varies significantly

### Don'ts

- **Don't use high-res textures** on mobile (use 2048px max)
- **Don't render too many HTML elements** (>100 hurts performance)
- **Don't block main thread** - load data async

## Resources

- [Globe.gl Documentation](https://globe.gl/)
- [Three.js Docs](https://threejs.org/docs/)
- [Globe.gl Examples](https://github.com/vasturiano/globe.gl/tree/master/example)
