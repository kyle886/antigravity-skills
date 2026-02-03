---
name: base44-migration
description: Patterns for migrating from Base44 SDK to direct Supabase integration. Use when modernizing Base44-dependent applications.
---

# Base44 Migration

Patterns and strategies for migrating applications from the Base44 SDK to direct Supabase integration.

## Use this skill when

- Migrating from @base44/sdk to Supabase client
- Replacing Base44 entity system with Supabase tables
- Converting Base44 auth to Supabase Auth
- Migrating Edge Functions from Base44 patterns
- Running parallel v1/v2 deployments

## Do not use this skill when

- Starting a new project (use `supabase-integration`)
- Not using Base44 SDK currently
- Migrating to a non-Supabase backend

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Migration Overview

### Phase 1: Audit & Inventory

```bash
# Find all Base44 SDK imports
grep -r "@base44/sdk" --include="*.js" --include="*.jsx" --include="*.ts" --include="*.tsx" src/

# Count entity usage
grep -roh "entities\.\w\+" src/ | sort | uniq -c | sort -rn

# Find auth calls
grep -r "base44\.auth" src/
```

### Phase 2: Entity Mapping

| Base44 Pattern                       | Supabase Equivalent                                         |
| ------------------------------------ | ----------------------------------------------------------- |
| `entities.Building.list()`           | `supabase.from('buildings').select()`                       |
| `entities.Building.get(id)`          | `supabase.from('buildings').select().eq('id', id).single()` |
| `entities.Building.create(data)`     | `supabase.from('buildings').insert(data).select().single()` |
| `entities.Building.update(id, data)` | `supabase.from('buildings').update(data).eq('id', id)`      |
| `entities.Building.delete(id)`       | `supabase.from('buildings').delete().eq('id', id)`          |

## Entity Migration

### Before (Base44)

```javascript
// Base44 SDK pattern
import base44 from "@base44/sdk";
base44.init(process.env.BASE44_APP_ID);

const { Building } = base44.entities;

// List with filters
const buildings = await Building.list({
  status: "active",
  sort: { field: "name", order: "asc" },
});

// Get single
const building = await Building.get("building-id");

// Create
const newBuilding = await Building.create({
  name: "New Tower",
  address: "123 Main St",
});

// Update
await Building.update("building-id", { name: "Updated Name" });

// Delete
await Building.delete("building-id");
```

### After (Supabase)

```typescript
// Direct Supabase client
import { supabase } from "@/lib/supabase";
import type { Tables, TablesInsert } from "@/lib/database.types";

type Building = Tables<"buildings">;

// List with filters
const { data: buildings, error } = await supabase
  .from("buildings")
  .select("*")
  .eq("status", "active")
  .order("name", { ascending: true });

// Get single
const { data: building, error } = await supabase
  .from("buildings")
  .select("*")
  .eq("id", "building-id")
  .single();

// Create
const { data: newBuilding, error } = await supabase
  .from("buildings")
  .insert({ name: "New Tower", address: "123 Main St" })
  .select()
  .single();

// Update
const { error } = await supabase
  .from("buildings")
  .update({ name: "Updated Name" })
  .eq("id", "building-id");

// Delete
const { error } = await supabase
  .from("buildings")
  .delete()
  .eq("id", "building-id");
```

## Auth Migration

### Before (Base44)

```javascript
import base44 from "@base44/sdk";

// Sign in
const user = await base44.auth.signIn(email, password);

// Get current user
const currentUser = base44.auth.currentUser;

// Sign out
await base44.auth.signOut();

// Auth state listener
base44.auth.onAuthStateChange((user) => {
  console.log("User:", user);
});
```

### After (Supabase)

```typescript
import { supabase } from "@/lib/supabase";

// Sign in
const { data, error } = await supabase.auth.signInWithPassword({
  email,
  password,
});

// Get current user
const {
  data: { user },
} = await supabase.auth.getUser();

// Sign out
await supabase.auth.signOut();

// Auth state listener
supabase.auth.onAuthStateChange((event, session) => {
  console.log("Event:", event, "User:", session?.user);
});
```

## Hook Migration

### Before (Base44)

```javascript
// Custom hook using Base44
function useBuildings() {
  const [buildings, setBuildings] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    entities.Building.list()
      .then(setBuildings)
      .finally(() => setLoading(false));
  }, []);

  return { buildings, loading };
}
```

### After (React Query + Supabase)

```typescript
// hooks/useBuildings.ts
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { supabase } from "@/lib/supabase";

export const buildingKeys = {
  all: ["buildings"] as const,
  list: () => [...buildingKeys.all, "list"] as const,
  detail: (id: string) => [...buildingKeys.all, id] as const,
};

export function useBuildings() {
  return useQuery({
    queryKey: buildingKeys.list(),
    queryFn: async () => {
      const { data, error } = await supabase
        .from("buildings")
        .select("*")
        .order("name");

      if (error) throw error;
      return data;
    },
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
      queryClient.invalidateQueries({ queryKey: buildingKeys.list() });
    },
  });
}
```

## Edge Function Migration

### Before (Base44 Edge Pattern)

```javascript
// supabase/functions/process-data/index.ts (Base44 style)
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { Base44SDK } from "@base44/sdk-deno";

serve(async (req) => {
  const sdk = new Base44SDK(Deno.env.get("BASE44_KEY"));

  const buildings = await sdk.entities.Building.list();

  return new Response(JSON.stringify(buildings));
});
```

### After (Direct Supabase)

```typescript
// supabase/functions/process-data/index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts";
import { createClient } from "https://esm.sh/@supabase/supabase-js@2";

serve(async (req) => {
  const supabase = createClient(
    Deno.env.get("SUPABASE_URL")!,
    Deno.env.get("SUPABASE_SERVICE_ROLE_KEY")!,
  );

  const { data: buildings, error } = await supabase
    .from("buildings")
    .select("*");

  if (error) {
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
    });
  }

  return new Response(JSON.stringify(buildings), {
    headers: { "Content-Type": "application/json" },
  });
});
```

## Parallel Deployment Strategy

### 1. Feature Flag Approach

```typescript
// lib/data-source.ts
const USE_V2 = process.env.NEXT_PUBLIC_USE_V2 === "true";

export async function getBuildings() {
  if (USE_V2) {
    // New Supabase implementation
    const { data } = await supabase.from("buildings").select("*");
    return data;
  } else {
    // Legacy Base44 implementation
    return entities.Building.list();
  }
}
```

### 2. Route-Based Cutover

```typescript
// Split routes between v1 and v2
const v2Routes = ['/buildings', '/leases', '/clients']

function App() {
  const location = useLocation()
  const isV2Route = v2Routes.some(r => location.pathname.startsWith(r))

  if (isV2Route) {
    return <V2App />  // New Supabase-based app
  }

  return <V1App />    // Legacy Base44-based app
}
```

### 3. User-Based Rollout

```typescript
// Gradually migrate users
const V2_USER_PERCENTAGE = 10;

function useDataSource() {
  const { user } = useAuth();

  // Consistent assignment based on user ID
  const isV2User = useMemo(() => {
    if (!user) return false;
    const hash = hashString(user.id);
    return hash % 100 < V2_USER_PERCENTAGE;
  }, [user?.id]);

  return isV2User ? supabaseClient : base44Client;
}
```

## Type Generation

```bash
# Generate types from existing Supabase schema
npx supabase gen types typescript --project-id $PROJECT_ID > src/lib/database.types.ts

# Add to package.json for CI
{
  "scripts": {
    "db:types": "supabase gen types typescript --project-id $PROJECT_ID > src/lib/database.types.ts",
    "prebuild": "npm run db:types"
  }
}
```

## Migration Checklist

### Pre-Migration

- [ ] Audit all `@base44/sdk` imports
- [ ] Document entity usage counts
- [ ] Map entities to Supabase tables
- [ ] Generate TypeScript types
- [ ] Set up RLS policies

### Migration

- [ ] Replace auth calls
- [ ] Convert entity operations to Supabase queries
- [ ] Migrate to React Query hooks
- [ ] Update Edge Functions
- [ ] Add error handling

### Post-Migration

- [ ] Remove `@base44/sdk` dependency
- [ ] Clean up unused code
- [ ] Update documentation
- [ ] Run integration tests
- [ ] Monitor error rates

## Common Pitfalls

### 1. Missing Error Handling

```typescript
// ❌ Base44 pattern (errors thrown)
const buildings = await entities.Building.list();

// ✅ Supabase pattern (errors in response)
const { data: buildings, error } = await supabase.from("buildings").select("*");

if (error) {
  console.error("Failed to fetch buildings:", error);
  throw error;
}
```

### 2. Missing Type Safety

```typescript
// ❌ Untyped query
const { data } = await supabase.from("buildings").select("*");

// ✅ Typed with generated types
import type { Tables } from "@/lib/database.types";
type Building = Tables<"buildings">;

const { data } = await supabase.from("buildings").select("*");
// data is Building[]
```

### 3. Auth Token Propagation

```typescript
// ❌ Using anon key for authenticated operations
const { data } = await supabase.from("private_data").select("*");

// ✅ Ensure user session is present
const {
  data: { session },
} = await supabase.auth.getSession();
if (!session) {
  throw new Error("Not authenticated");
}
// RLS will now work correctly
const { data } = await supabase.from("private_data").select("*");
```

## Resources

- [Supabase Migration Guide](https://supabase.com/docs/guides/migrations)
- [supabase-integration skill](file:///supabase-integration/SKILL.md)
- [TanStack Query Documentation](https://tanstack.com/query)
