---
name: supabase-integration
description: Master direct Supabase integration with TypeScript, including client setup, authentication, RLS policies, type generation, realtime subscriptions, and Edge Functions. Use when building applications with Supabase as the backend.
---

# Supabase Integration

Comprehensive patterns for direct Supabase integration in TypeScript applications, replacing SDK abstractions with type-safe database access.

## Use this skill when

- Setting up Supabase client in React/Next.js applications
- Implementing authentication flows with Supabase Auth
- Writing Row Level Security (RLS) policies
- Generating TypeScript types from database schema
- Building realtime features with subscriptions
- Creating and invoking Edge Functions
- Managing file storage with Supabase Storage

## Do not use this skill when

- Using a different database provider
- Working with ORMs like Prisma (use `postgresql` skill instead)
- Building mobile-native apps (consider Flutter/React Native skills)

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Core Concepts

### 1. Client Setup

```typescript
// lib/supabase.ts
import { createClient } from "@supabase/supabase-js";
import type { Database } from "./database.types";

// Browser client (anon key - safe for client-side)
export const supabase = createClient<Database>(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
);

// Server client (service role - NEVER expose to client)
export const supabaseAdmin = createClient<Database>(
  process.env.SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY!,
  {
    auth: {
      autoRefreshToken: false,
      persistSession: false,
    },
  },
);
```

### 2. Type Generation

```bash
# Generate types from remote database
npx supabase gen types typescript --project-id YOUR_PROJECT_ID > src/lib/database.types.ts

# Generate from local database (after supabase start)
npx supabase gen types typescript --local > src/lib/database.types.ts

# Add to package.json scripts
{
  "scripts": {
    "db:types": "supabase gen types typescript --project-id $PROJECT_ID > src/lib/database.types.ts"
  }
}
```

### 3. Type-Safe Queries

```typescript
import { supabase } from "@/lib/supabase";
import type { Tables, TablesInsert, TablesUpdate } from "@/lib/database.types";

// Inferred types from generated schema
type Building = Tables<"buildings">;
type BuildingInsert = TablesInsert<"buildings">;
type BuildingUpdate = TablesUpdate<"buildings">;

// SELECT with relations
async function getBuildingsWithLeases() {
  const { data, error } = await supabase
    .from("buildings")
    .select(
      `
      id,
      name,
      address,
      leases (
        id,
        tenant_name,
        start_date,
        end_date
      )
    `,
    )
    .eq("status", "active")
    .order("name");

  if (error) throw error;
  return data; // Fully typed!
}

// INSERT
async function createBuilding(building: BuildingInsert) {
  const { data, error } = await supabase
    .from("buildings")
    .insert(building)
    .select()
    .single();

  if (error) throw error;
  return data;
}

// UPDATE
async function updateBuilding(id: string, updates: BuildingUpdate) {
  const { data, error } = await supabase
    .from("buildings")
    .update(updates)
    .eq("id", id)
    .select()
    .single();

  if (error) throw error;
  return data;
}

// DELETE
async function deleteBuilding(id: string) {
  const { error } = await supabase.from("buildings").delete().eq("id", id);

  if (error) throw error;
}
```

## Authentication Patterns

### 1. Auth Setup

```typescript
// lib/auth.ts
import { supabase } from "./supabase";

export async function signInWithEmail(email: string, password: string) {
  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password,
  });
  if (error) throw error;
  return data;
}

export async function signUp(email: string, password: string) {
  const { data, error } = await supabase.auth.signUp({
    email,
    password,
    options: {
      emailRedirectTo: `${window.location.origin}/auth/callback`,
    },
  });
  if (error) throw error;
  return data;
}

export async function signOut() {
  const { error } = await supabase.auth.signOut();
  if (error) throw error;
}

export async function getSession() {
  const {
    data: { session },
    error,
  } = await supabase.auth.getSession();
  if (error) throw error;
  return session;
}
```

### 2. Auth State Hook

```typescript
// hooks/useAuth.ts
import { useEffect, useState } from "react";
import { supabase } from "@/lib/supabase";
import type { User, Session } from "@supabase/supabase-js";

export function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  const [session, setSession] = useState<Session | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Get initial session
    supabase.auth.getSession().then(({ data: { session } }) => {
      setSession(session);
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const {
      data: { subscription },
    } = supabase.auth.onAuthStateChange((_event, session) => {
      setSession(session);
      setUser(session?.user ?? null);
      setLoading(false);
    });

    return () => subscription.unsubscribe();
  }, []);

  return { user, session, loading };
}
```

### 3. Protected Route Component

```typescript
// components/ProtectedRoute.tsx
import { useAuth } from '@/hooks/useAuth'
import { Navigate, useLocation } from 'react-router-dom'

export function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { user, loading } = useAuth()
  const location = useLocation()

  if (loading) {
    return <div>Loading...</div>
  }

  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />
  }

  return <>{children}</>
}
```

## Row Level Security (RLS)

### 1. Basic Policies

```sql
-- Enable RLS on table
ALTER TABLE buildings ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their own buildings
CREATE POLICY "Users can view own buildings"
ON buildings FOR SELECT
TO authenticated
USING (user_id = auth.uid());

-- Policy: Users can insert their own buildings
CREATE POLICY "Users can insert own buildings"
ON buildings FOR INSERT
TO authenticated
WITH CHECK (user_id = auth.uid());

-- Policy: Users can update their own buildings
CREATE POLICY "Users can update own buildings"
ON buildings FOR UPDATE
TO authenticated
USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());

-- Policy: Users can delete their own buildings
CREATE POLICY "Users can delete own buildings"
ON buildings FOR DELETE
TO authenticated
USING (user_id = auth.uid());
```

### 2. Organization-Based Access

```sql
-- Policy: Users can access buildings in their organization
CREATE POLICY "Org members can view buildings"
ON buildings FOR SELECT
TO authenticated
USING (
  organization_id IN (
    SELECT organization_id FROM organization_members
    WHERE user_id = auth.uid()
  )
);

-- Policy: Only admins can delete
CREATE POLICY "Admins can delete buildings"
ON buildings FOR DELETE
TO authenticated
USING (
  EXISTS (
    SELECT 1 FROM organization_members
    WHERE user_id = auth.uid()
    AND organization_id = buildings.organization_id
    AND role = 'admin'
  )
);
```

### 3. Testing RLS Policies

```sql
-- Test as a specific user
SET request.jwt.claim.sub = 'user-uuid-here';
SET role TO authenticated;

-- Run your query
SELECT * FROM buildings;

-- Reset
RESET role;
```

## Realtime Subscriptions

### 1. Basic Subscription

```typescript
// Subscribe to changes on a table
const channel = supabase
  .channel("buildings-changes")
  .on(
    "postgres_changes",
    {
      event: "*", // INSERT, UPDATE, DELETE, or *
      schema: "public",
      table: "buildings",
    },
    (payload) => {
      console.log("Change received!", payload);
      // payload.new - new record (for INSERT/UPDATE)
      // payload.old - old record (for UPDATE/DELETE)
      // payload.eventType - 'INSERT' | 'UPDATE' | 'DELETE'
    },
  )
  .subscribe();

// Cleanup
channel.unsubscribe();
```

### 2. Realtime Hook

```typescript
// hooks/useRealtimeBuildings.ts
import { useEffect, useState } from "react";
import { supabase } from "@/lib/supabase";
import type { Tables } from "@/lib/database.types";

type Building = Tables<"buildings">;

export function useRealtimeBuildings() {
  const [buildings, setBuildings] = useState<Building[]>([]);

  useEffect(() => {
    // Initial fetch
    supabase
      .from("buildings")
      .select("*")
      .then(({ data }) => setBuildings(data ?? []));

    // Subscribe to changes
    const channel = supabase
      .channel("buildings-realtime")
      .on(
        "postgres_changes",
        { event: "*", schema: "public", table: "buildings" },
        (payload) => {
          if (payload.eventType === "INSERT") {
            setBuildings((prev) => [...prev, payload.new as Building]);
          } else if (payload.eventType === "UPDATE") {
            setBuildings((prev) =>
              prev.map((b) =>
                b.id === (payload.new as Building).id
                  ? (payload.new as Building)
                  : b,
              ),
            );
          } else if (payload.eventType === "DELETE") {
            setBuildings((prev) =>
              prev.filter((b) => b.id !== (payload.old as Building).id),
            );
          }
        },
      )
      .subscribe();

    return () => {
      channel.unsubscribe();
    };
  }, []);

  return buildings;
}
```

## Edge Functions

### 1. Creating an Edge Function

```typescript
// supabase/functions/send-notification/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

const corsHeaders = {
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Headers":
    "authorization, x-client-info, apikey, content-type",
};

serve(async (req) => {
  // Handle CORS preflight
  if (req.method === "OPTIONS") {
    return new Response("ok", { headers: corsHeaders });
  }

  try {
    // Create Supabase client with service role
    const supabase = createClient(
      Deno.env.get("SUPABASE_URL")!,
      Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!,
    );

    // Get request body
    const { userId, message } = await req.json();

    // Your logic here
    const { error } = await supabase
      .from("notifications")
      .insert({ user_id: userId, message });

    if (error) throw error;

    return new Response(JSON.stringify({ success: true }), {
      headers: { ...corsHeaders, "Content-Type": "application/json" },
    });
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 400,
      headers: { ...corsHeaders, "Content-Type": "application/json" },
    });
  }
});
```

### 2. Invoking Edge Functions

```typescript
// From client
const { data, error } = await supabase.functions.invoke("send-notification", {
  body: { userId: "user-123", message: "Hello!" },
});

// With custom headers
const { data, error } = await supabase.functions.invoke("my-function", {
  body: { foo: "bar" },
  headers: { "x-custom-header": "value" },
});
```

## Storage

### 1. Upload Files

```typescript
// Upload a file
async function uploadFile(bucket: string, path: string, file: File) {
  const { data, error } = await supabase.storage
    .from(bucket)
    .upload(path, file, {
      cacheControl: "3600",
      upsert: false,
    });

  if (error) throw error;
  return data;
}

// Get public URL
function getPublicUrl(bucket: string, path: string) {
  const { data } = supabase.storage.from(bucket).getPublicUrl(path);

  return data.publicUrl;
}

// Get signed URL (for private files)
async function getSignedUrl(bucket: string, path: string, expiresIn = 3600) {
  const { data, error } = await supabase.storage
    .from(bucket)
    .createSignedUrl(path, expiresIn);

  if (error) throw error;
  return data.signedUrl;
}
```

## TanStack Query Integration

```typescript
// hooks/useBuildings.ts
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { supabase } from "@/lib/supabase";
import type { Tables, TablesInsert } from "@/lib/database.types";

type Building = Tables<"buildings">;

export const buildingKeys = {
  all: ["buildings"] as const,
  lists: () => [...buildingKeys.all, "list"] as const,
  list: (filters: Record<string, unknown>) =>
    [...buildingKeys.lists(), filters] as const,
  details: () => [...buildingKeys.all, "detail"] as const,
  detail: (id: string) => [...buildingKeys.details(), id] as const,
};

export function useBuildings(filters?: { status?: string }) {
  return useQuery({
    queryKey: buildingKeys.list(filters ?? {}),
    queryFn: async () => {
      let query = supabase.from("buildings").select("*");

      if (filters?.status) {
        query = query.eq("status", filters.status);
      }

      const { data, error } = await query;
      if (error) throw error;
      return data;
    },
  });
}

export function useBuilding(id: string) {
  return useQuery({
    queryKey: buildingKeys.detail(id),
    queryFn: async () => {
      const { data, error } = await supabase
        .from("buildings")
        .select("*, leases(*)")
        .eq("id", id)
        .single();

      if (error) throw error;
      return data;
    },
    enabled: !!id,
  });
}

export function useCreateBuilding() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: async (building: TablesInsert<"buildings">) => {
      const { data, error } = await supabase
        .from("buildings")
        .insert(building)
        .select()
        .single();

      if (error) throw error;
      return data;
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: buildingKeys.lists() });
    },
  });
}
```

## Best Practices

### Do's

- **Always generate types** from your database schema
- **Enable RLS** on all tables with sensitive data
- **Use service role** only on server-side
- **Handle errors** gracefully with proper user feedback
- **Use realtime** sparingly - it has connection limits

### Don'ts

- **Never expose service role key** to the client
- **Don't skip RLS** - it's your security layer
- **Avoid N+1 queries** - use Supabase's relation syntax
- **Don't store secrets** in client-accessible tables

## Resources

- [Supabase Documentation](https://supabase.com/docs)
- [Supabase TypeScript Guide](https://supabase.com/docs/guides/api/rest/generating-types)
- [RLS Guide](https://supabase.com/docs/guides/auth/row-level-security)
- [Realtime Documentation](https://supabase.com/docs/guides/realtime)
