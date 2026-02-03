---
name: vite-modern-patterns
description: Master Vite build tool configuration, plugins, optimization, and deployment patterns. Use when setting up or optimizing Vite-based React/Vue/Svelte projects.
---

# Vite Modern Patterns

Comprehensive guide to configuring and optimizing Vite for modern frontend development.

## Use this skill when

- Setting up new Vite projects
- Configuring build optimization
- Adding Vite plugins
- Setting up environment variables
- Configuring proxy for development
- Optimizing production builds
- Setting up Vitest for testing

## Do not use this skill when

- Using Webpack or other bundlers
- Building Node.js backends
- Working with legacy CJS-only codebases

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Project Setup

### 1. Create New Project

```bash
# React + TypeScript (recommended)
npm create vite@latest my-app -- --template react-ts

# React + SWC (faster compilation)
npm create vite@latest my-app -- --template react-swc-ts

# Vue + TypeScript
npm create vite@latest my-app -- --template vue-ts

# Vanilla TypeScript
npm create vite@latest my-app -- --template vanilla-ts
```

### 2. Project Structure

```
my-app/
├── public/              # Static assets (copied as-is)
│   └── favicon.ico
├── src/
│   ├── assets/          # Assets processed by Vite
│   ├── components/
│   ├── lib/
│   ├── pages/
│   ├── App.tsx
│   ├── main.tsx
│   └── vite-env.d.ts    # Vite type definitions
├── index.html           # Entry HTML (Vite uses this)
├── package.json
├── tsconfig.json
├── tsconfig.node.json   # Config for vite.config.ts
└── vite.config.ts
```

## Configuration

### 1. Basic Configuration

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "path";

export default defineConfig({
  plugins: [react()],

  // Path aliases
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
      "@components": path.resolve(__dirname, "./src/components"),
      "@lib": path.resolve(__dirname, "./src/lib"),
    },
  },

  // Development server
  server: {
    port: 3000,
    open: true,
    cors: true,
  },

  // Preview server (production preview)
  preview: {
    port: 4173,
  },

  // Build options
  build: {
    outDir: "dist",
    sourcemap: true,
    minify: "terser",
  },
});
```

### 2. TypeScript Path Aliases

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@lib/*": ["./src/lib/*"]
    }
  }
}
```

## Environment Variables

### 1. Environment Files

```bash
.env                # Loaded in all cases
.env.local          # Loaded in all cases, ignored by git
.env.development    # Only loaded in development
.env.production     # Only loaded in production
.env.staging        # Custom mode: vite build --mode staging
```

### 2. Variable Usage

```bash
# .env
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App

# Non-VITE_ prefix = server-side only (not exposed to client)
DATABASE_URL=postgres://...
```

```typescript
// Access in code (only VITE_ prefix exposed)
const apiUrl = import.meta.env.VITE_API_URL;
const isDev = import.meta.env.DEV;
const isProd = import.meta.env.PROD;
const mode = import.meta.env.MODE; // 'development' | 'production' | custom

// Type-safe environment
// src/vite-env.d.ts
/// <reference types="vite/client" />
interface ImportMetaEnv {
  readonly VITE_API_URL: string;
  readonly VITE_APP_TITLE: string;
}
interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

## Plugins

### 1. Essential Plugins

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [
    react(),
    tsconfigPaths(), // Auto-resolve tsconfig paths
  ],
});
```

### 2. Common Plugin Configurations

```typescript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { visualizer } from "rollup-plugin-visualizer";
import { VitePWA } from "vite-plugin-pwa";
import svgr from "vite-plugin-svgr";

export default defineConfig({
  plugins: [
    react(),

    // SVG as React components
    svgr({
      svgrOptions: {
        icon: true,
      },
    }),

    // PWA support
    VitePWA({
      registerType: "autoUpdate",
      manifest: {
        name: "My App",
        short_name: "App",
        theme_color: "#ffffff",
      },
    }),

    // Bundle analyzer (only in build)
    visualizer({
      filename: "dist/stats.html",
      open: true,
      gzipSize: true,
    }),
  ],
});
```

## Development Server

### 1. Proxy Configuration

```typescript
// vite.config.ts
export default defineConfig({
  server: {
    proxy: {
      // Simple proxy
      "/api": "http://localhost:8080",

      // With rewrite
      "/api": {
        target: "http://localhost:8080",
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ""),
      },

      // WebSocket proxy
      "/socket.io": {
        target: "ws://localhost:8080",
        ws: true,
      },
    },
  },
});
```

### 2. HTTPS Development

```typescript
import fs from "fs";

export default defineConfig({
  server: {
    https: {
      key: fs.readFileSync("certs/localhost-key.pem"),
      cert: fs.readFileSync("certs/localhost.pem"),
    },
  },
});

// Or use vite-plugin-mkcert for auto-generated certs
import mkcert from "vite-plugin-mkcert";

export default defineConfig({
  plugins: [mkcert()],
  server: {
    https: true,
  },
});
```

## Build Optimization

### 1. Code Splitting

```typescript
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // Vendor chunk
          vendor: ["react", "react-dom", "react-router-dom"],

          // UI library chunk
          ui: ["@radix-ui/react-dialog", "@radix-ui/react-dropdown-menu"],

          // Charting chunk
          charts: ["recharts"],
        },
      },
    },
  },
});

// Dynamic import for code splitting
const LazyComponent = lazy(() => import("./components/HeavyComponent"));
```

### 2. Dependency Optimization

```typescript
export default defineConfig({
  optimizeDeps: {
    // Pre-bundle these dependencies
    include: ["react", "react-dom", "lodash-es"],

    // Exclude from pre-bundling
    exclude: ["@my-org/local-package"],
  },

  build: {
    // Target modern browsers
    target: "esnext",

    // Terser options
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },

    // Chunk size warning limit
    chunkSizeWarningLimit: 1000,
  },
});
```

### 3. Asset Handling

```typescript
export default defineConfig({
  build: {
    // Assets smaller than this are inlined as base64
    assetsInlineLimit: 4096,

    // Asset file naming
    rollupOptions: {
      output: {
        assetFileNames: "assets/[name]-[hash][extname]",
        chunkFileNames: "js/[name]-[hash].js",
        entryFileNames: "js/[name]-[hash].js",
      },
    },
  },
});
```

## Testing with Vitest

### 1. Setup

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/test/setup.ts",
    css: true,
    coverage: {
      provider: "v8",
      reporter: ["text", "html"],
    },
  },
});
```

```typescript
// src/test/setup.ts
import "@testing-library/jest-dom";
import { afterEach } from "vitest";
import { cleanup } from "@testing-library/react";

afterEach(() => {
  cleanup();
});
```

### 2. Test Example

```typescript
// src/components/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { Button } from './Button'

describe('Button', () => {
  it('renders with text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button')).toHaveTextContent('Click me')
  })

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>Click</Button>)
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledOnce()
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Disabled</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
})
```

## Performance Patterns

### 1. Conditional Imports

```typescript
// Only load heavy libraries in production
if (import.meta.env.PROD) {
  import("./analytics").then(({ init }) => init());
}

// Environment-specific code (tree-shaken in production)
if (import.meta.env.DEV) {
  console.log("Development mode");
}
```

### 2. Web Workers

```typescript
// worker.ts
self.onmessage = (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
};

// main.ts
import Worker from "./worker?worker";

const worker = new Worker();
worker.postMessage(data);
worker.onmessage = (e) => {
  console.log("Result:", e.data);
};
```

### 3. Static Asset Imports

```typescript
// Import as URL
import imgUrl from "./image.png";
// imgUrl = '/assets/image-abc123.png'

// Import as raw string
import content from "./file.txt?raw";

// Import as URL (explicit)
import workletUrl from "./worklet.js?url";
```

## Deployment

### 1. Static Hosting (Cloudflare Pages, Netlify, Vercel)

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    outDir: "dist",
  },
});
```

```bash
# Build
npm run build

# Preview locally
npm run preview
```

### 2. Base URL Configuration

```typescript
// For subdirectory deployment
export default defineConfig({
  base: "/my-app/",
});

// For GitHub Pages
export default defineConfig({
  base: process.env.NODE_ENV === "production" ? "/repo-name/" : "/",
});
```

### 3. SPA Fallback

```json
// For Cloudflare Pages: _redirects
/*    /index.html   200

// For Netlify: netlify.toml
[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

## Best Practices

### Do's

- **Use path aliases** for cleaner imports
- **Pre-bundle heavy deps** with `optimizeDeps.include`
- **Split vendor chunks** for better caching
- **Enable source maps** for debugging
- **Use environment variables** for configuration

### Don'ts

- **Don't import from `node_modules` directly** in `public/`
- **Don't use `require()`** - Vite is ESM-first
- **Don't ignore TypeScript errors** in config
- **Don't bundle everything** - use dynamic imports
- **Don't commit `.env.local`** files

## Resources

- [Vite Documentation](https://vitejs.dev)
- [Vitest Documentation](https://vitest.dev)
- [Awesome Vite](https://github.com/vitejs/awesome-vite)
- [Vite Plugin Directory](https://vitejs.dev/plugins/)
