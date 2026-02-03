---
name: cloudflare-workers
description: Build and deploy edge applications with Cloudflare Workers, Pages Functions, KV, D1, and Durable Objects. Use when deploying to Cloudflare's edge network or building serverless APIs.
---

# Cloudflare Workers

Comprehensive guide to building edge applications with Cloudflare Workers, Pages, and associated services.

## Use this skill when

- Deploying React/Vue/Svelte apps to Cloudflare Pages
- Building serverless APIs with Workers
- Implementing edge caching with KV
- Using D1 (SQLite at the edge)
- Building stateful applications with Durable Objects
- Setting up CI/CD with Wrangler

## Do not use this skill when

- Deploying to other platforms (Vercel, Netlify)
- Building traditional server applications
- Using AWS Lambda or similar (use cloud-specific skills)

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Core Concepts

### 1. Worker Basics

```typescript
// src/index.ts - Basic Worker
export default {
  async fetch(
    request: Request,
    env: Env,
    ctx: ExecutionContext,
  ): Promise<Response> {
    const url = new URL(request.url);

    if (url.pathname === "/api/hello") {
      return new Response(JSON.stringify({ message: "Hello from the edge!" }), {
        headers: { "Content-Type": "application/json" },
      });
    }

    return new Response("Not Found", { status: 404 });
  },
};

interface Env {
  // Environment bindings
  MY_KV_NAMESPACE: KVNamespace;
  MY_D1_DATABASE: D1Database;
  API_KEY: string;
}
```

### 2. Wrangler Configuration

```toml
# wrangler.toml
name = "my-worker"
main = "src/index.ts"
compatibility_date = "2024-01-01"

# Environment variables
[vars]
ENVIRONMENT = "production"

# KV Namespace binding
[[kv_namespaces]]
binding = "MY_KV"
id = "abc123..."

# D1 Database binding
[[d1_databases]]
binding = "DB"
database_name = "my-database"
database_id = "xyz789..."

# Secrets (set via `wrangler secret put`)
# API_KEY = "..."

# Routes
[env.production]
routes = [
  { pattern = "api.example.com/*", zone_name = "example.com" }
]
```

## Pages Functions

### 1. File-Based Routing

```
functions/
├── api/
│   ├── users/
│   │   ├── index.ts        # GET/POST /api/users
│   │   └── [id].ts         # GET/PUT/DELETE /api/users/:id
│   └── health.ts           # GET /api/health
└── _middleware.ts          # Runs on all routes
```

### 2. Pages Function Handler

```typescript
// functions/api/users/[id].ts
import type { PagesFunction, EventContext } from "@cloudflare/workers-types";

interface Env {
  DB: D1Database;
}

export const onRequestGet: PagesFunction<Env> = async (context) => {
  const { id } = context.params;

  const user = await context.env.DB.prepare("SELECT * FROM users WHERE id = ?")
    .bind(id)
    .first();

  if (!user) {
    return new Response("User not found", { status: 404 });
  }

  return Response.json(user);
};

export const onRequestPut: PagesFunction<Env> = async (context) => {
  const { id } = context.params;
  const body = await context.request.json();

  await context.env.DB.prepare(
    "UPDATE users SET name = ?, email = ? WHERE id = ?",
  )
    .bind(body.name, body.email, id)
    .run();

  return Response.json({ success: true });
};

export const onRequestDelete: PagesFunction<Env> = async (context) => {
  const { id } = context.params;

  await context.env.DB.prepare("DELETE FROM users WHERE id = ?").bind(id).run();

  return new Response(null, { status: 204 });
};
```

### 3. Middleware

```typescript
// functions/_middleware.ts
import type { PagesFunction } from "@cloudflare/workers-types";

// CORS middleware
const corsHeaders = {
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type, Authorization",
};

export const onRequest: PagesFunction = async (context) => {
  // Handle CORS preflight
  if (context.request.method === "OPTIONS") {
    return new Response(null, { headers: corsHeaders });
  }

  // Add CORS headers to response
  const response = await context.next();

  Object.entries(corsHeaders).forEach(([key, value]) => {
    response.headers.set(key, value);
  });

  return response;
};
```

## KV Storage

### 1. Basic Operations

```typescript
interface Env {
  CACHE: KVNamespace;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // GET
    const value = await env.CACHE.get("my-key");
    const jsonValue = await env.CACHE.get("my-json-key", "json");

    // PUT with expiration
    await env.CACHE.put("my-key", "my-value", {
      expirationTtl: 3600, // 1 hour
    });

    // PUT with metadata
    await env.CACHE.put("my-key", "my-value", {
      metadata: { timestamp: Date.now() },
    });

    // GET with metadata
    const { value: val, metadata } = await env.CACHE.getWithMetadata("my-key");

    // DELETE
    await env.CACHE.delete("my-key");

    // LIST keys
    const keys = await env.CACHE.list({ prefix: "user:" });

    return new Response("OK");
  },
};
```

### 2. Caching Pattern

```typescript
async function getCachedData<T>(
  kv: KVNamespace,
  key: string,
  fetcher: () => Promise<T>,
  ttl = 3600,
): Promise<T> {
  // Try cache first
  const cached = (await kv.get(key, "json")) as T | null;
  if (cached) return cached;

  // Fetch fresh data
  const data = await fetcher();

  // Store in cache (non-blocking)
  await kv.put(key, JSON.stringify(data), { expirationTtl: ttl });

  return data;
}

// Usage
const user = await getCachedData(
  env.CACHE,
  `user:${userId}`,
  () => fetchUserFromDB(userId),
  3600,
);
```

## D1 Database

### 1. Schema and Migrations

```sql
-- migrations/0001_create_users.sql
CREATE TABLE IF NOT EXISTS users (
  id TEXT PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
```

```bash
# Run migrations
wrangler d1 migrations apply my-database

# Execute SQL directly
wrangler d1 execute my-database --command="SELECT * FROM users"
```

### 2. D1 Queries

```typescript
interface Env {
  DB: D1Database;
}

// Select all
const { results } = await env.DB.prepare("SELECT * FROM users WHERE active = ?")
  .bind(true)
  .all();

// Select one
const user = await env.DB.prepare("SELECT * FROM users WHERE id = ?")
  .bind(userId)
  .first();

// Insert
const result = await env.DB.prepare(
  "INSERT INTO users (id, email, name) VALUES (?, ?, ?)",
)
  .bind(crypto.randomUUID(), email, name)
  .run();

// Batch operations
const batch = await env.DB.batch([
  env.DB.prepare("INSERT INTO users (id, name) VALUES (?, ?)").bind(
    "1",
    "Alice",
  ),
  env.DB.prepare("INSERT INTO users (id, name) VALUES (?, ?)").bind("2", "Bob"),
]);
```

### 3. Type-Safe D1 with Drizzle

```typescript
// schema.ts
import { sqliteTable, text, integer } from "drizzle-orm/sqlite-core";

export const users = sqliteTable("users", {
  id: text("id").primaryKey(),
  email: text("email").notNull().unique(),
  name: text("name").notNull(),
  createdAt: integer("created_at", { mode: "timestamp" }),
});

// index.ts
import { drizzle } from "drizzle-orm/d1";
import * as schema from "./schema";

export default {
  async fetch(request: Request, env: Env) {
    const db = drizzle(env.DB, { schema });

    const allUsers = await db.select().from(schema.users);

    const user = await db
      .insert(schema.users)
      .values({
        id: crypto.randomUUID(),
        email: "test@example.com",
        name: "Test User",
      })
      .returning();

    return Response.json(allUsers);
  },
};
```

## Durable Objects

### 1. Counter Example

```typescript
// src/counter.ts
export class Counter {
  state: DurableObjectState;
  value: number = 0;

  constructor(state: DurableObjectState) {
    this.state = state;
    // Load persisted value
    this.state.blockConcurrencyWhile(async () => {
      const stored = await this.state.storage.get<number>("value");
      this.value = stored ?? 0;
    });
  }

  async fetch(request: Request): Promise<Response> {
    const url = new URL(request.url);

    switch (url.pathname) {
      case "/increment":
        this.value++;
        await this.state.storage.put("value", this.value);
        return new Response(String(this.value));

      case "/decrement":
        this.value--;
        await this.state.storage.put("value", this.value);
        return new Response(String(this.value));

      case "/value":
        return new Response(String(this.value));

      default:
        return new Response("Not found", { status: 404 });
    }
  }
}
```

```toml
# wrangler.toml
[[durable_objects.bindings]]
name = "COUNTER"
class_name = "Counter"

[[migrations]]
tag = "v1"
new_classes = ["Counter"]
```

### 2. Using Durable Objects

```typescript
interface Env {
  COUNTER: DurableObjectNamespace;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // Get a unique ID (same ID = same instance)
    const id = env.COUNTER.idFromName("global-counter");

    // Get the Durable Object stub
    const counter = env.COUNTER.get(id);

    // Forward the request
    return counter.fetch(request);
  },
};
```

## Deploying React with Pages

### 1. Project Setup

```bash
# Create Vite + React project
npm create vite@latest my-app -- --template react-ts
cd my-app

# Add Pages Functions
mkdir -p functions/api
```

### 2. Vite Configuration

```typescript
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  build: {
    outDir: "dist",
  },
});
```

### 3. Deploy Commands

```bash
# Local development
wrangler pages dev dist --live-reload

# Deploy to preview
wrangler pages deploy dist

# Deploy to production
wrangler pages deploy dist --branch main
```

## Environment & Secrets

```bash
# Set secret (production)
wrangler secret put API_KEY

# Set secret for Pages
wrangler pages secret put API_KEY --project-name my-project

# Local development (.dev.vars)
echo 'API_KEY=my-local-key' > .dev.vars
```

```typescript
// Accessing environment
interface Env {
  API_KEY: string; // Secret
  ENVIRONMENT: string; // Variable from wrangler.toml
  DB: D1Database; // Binding
}

export default {
  async fetch(request: Request, env: Env) {
    console.log(env.API_KEY); // Secret value
    console.log(env.ENVIRONMENT); // 'production'
  },
};
```

## Best Practices

### Do's

- **Use bindings** for KV, D1, R2 instead of REST APIs
- **Return early** for common paths (CORS, 404)
- **Use `ctx.waitUntil()`** for background tasks
- **Cache aggressively** with KV for read-heavy workloads
- **Type your environment** with TypeScript interfaces

### Don'ts

- **Don't use `node:` modules** without compatibility flags
- **Don't block on slow operations** - use streams
- **Don't store large blobs in KV** - use R2 instead
- **Don't make synchronous calls** to external services

## Resources

- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/)
- [Pages Functions](https://developers.cloudflare.com/pages/functions/)
- [D1 Documentation](https://developers.cloudflare.com/d1/)
- [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/)
