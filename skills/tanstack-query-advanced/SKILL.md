---
name: tanstack-query-advanced
description: Advanced TanStack Query patterns including prefetching, infinite queries, optimistic updates, suspense mode, and testing. Use for complex server state management beyond basic data fetching.
---

# TanStack Query Advanced Patterns

Advanced patterns for TanStack Query (React Query) covering prefetching, infinite queries, optimistic updates, and testing strategies.

## Use this skill when

- Implementing SSR/SSG with query prefetching
- Building infinite scroll or pagination
- Creating dependent/sequential queries
- Implementing optimistic updates
- Using React Suspense with queries
- Testing components that use queries

## Do not use this skill when

- Basic CRUD operations (see `react-state-management`)
- Client-only state management
- Using SWR or other libraries

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Core Concepts

### Query Key Factory

```typescript
// lib/query-keys.ts
export const buildingKeys = {
  all: ["buildings"] as const,
  lists: () => [...buildingKeys.all, "list"] as const,
  list: (filters: BuildingFilters) =>
    [...buildingKeys.lists(), filters] as const,
  details: () => [...buildingKeys.all, "detail"] as const,
  detail: (id: string) => [...buildingKeys.details(), id] as const,
  leases: (id: string) => [...buildingKeys.detail(id), "leases"] as const,
};

// Usage
queryClient.invalidateQueries({ queryKey: buildingKeys.lists() });
queryClient.invalidateQueries({ queryKey: buildingKeys.detail("123") });
```

## Prefetching Patterns

### 1. Route-Based Prefetching

```typescript
// React Router loader
import { QueryClient } from '@tanstack/react-query'

const queryClient = new QueryClient()

export const buildingLoader = async ({ params }) => {
  const { id } = params

  // Prefetch or return cached data
  await queryClient.ensureQueryData({
    queryKey: buildingKeys.detail(id),
    queryFn: () => fetchBuilding(id),
    staleTime: 5 * 60 * 1000, // 5 minutes
  })

  return null
}

// Route config
const router = createBrowserRouter([
  {
    path: '/buildings/:id',
    element: <BuildingDetails />,
    loader: buildingLoader,
  },
])
```

### 2. Hover Prefetching

```typescript
function BuildingListItem({ building }) {
  const queryClient = useQueryClient()

  const prefetchBuilding = () => {
    queryClient.prefetchQuery({
      queryKey: buildingKeys.detail(building.id),
      queryFn: () => fetchBuilding(building.id),
      staleTime: 60 * 1000, // 1 minute
    })
  }

  return (
    <Link
      to={`/buildings/${building.id}`}
      onMouseEnter={prefetchBuilding}
      onFocus={prefetchBuilding}
    >
      {building.name}
    </Link>
  )
}
```

### 3. SSR Prefetching (Next.js)

```typescript
// pages/buildings/[id].tsx
import { dehydrate, QueryClient } from '@tanstack/react-query'

export async function getServerSideProps({ params }) {
  const queryClient = new QueryClient()

  await queryClient.prefetchQuery({
    queryKey: buildingKeys.detail(params.id),
    queryFn: () => fetchBuilding(params.id),
  })

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  }
}

// _app.tsx
import { HydrationBoundary } from '@tanstack/react-query'

function App({ Component, pageProps }) {
  return (
    <QueryClientProvider client={queryClient}>
      <HydrationBoundary state={pageProps.dehydratedState}>
        <Component {...pageProps} />
      </HydrationBoundary>
    </QueryClientProvider>
  )
}
```

## Infinite Queries

### 1. Basic Infinite Query

```typescript
interface BuildingsPage {
  data: Building[]
  nextCursor: string | null
}

function useInfiniteBuildings(filters: BuildingFilters) {
  return useInfiniteQuery({
    queryKey: buildingKeys.list(filters),
    queryFn: async ({ pageParam }): Promise<BuildingsPage> => {
      const response = await fetch(
        `/api/buildings?cursor=${pageParam}&limit=20`
      )
      return response.json()
    },
    initialPageParam: '',
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    staleTime: 5 * 60 * 1000,
  })
}

// Component usage
function BuildingsList() {
  const {
    data,
    fetchNextPage,
    hasNextPage,
    isFetchingNextPage,
    status,
  } = useInfiniteBuildings({})

  const buildings = data?.pages.flatMap((page) => page.data) ?? []

  return (
    <div>
      {buildings.map((building) => (
        <BuildingCard key={building.id} building={building} />
      ))}

      {hasNextPage && (
        <Button
          onClick={() => fetchNextPage()}
          disabled={isFetchingNextPage}
        >
          {isFetchingNextPage ? 'Loading...' : 'Load More'}
        </Button>
      )}
    </div>
  )
}
```

### 2. Intersection Observer Integration

```typescript
function useIntersectionObserver({
  target,
  onIntersect,
  enabled = true,
}: {
  target: React.RefObject<Element>
  onIntersect: () => void
  enabled?: boolean
}) {
  useEffect(() => {
    if (!enabled || !target.current) return

    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          onIntersect()
        }
      },
      { threshold: 0.1 }
    )

    observer.observe(target.current)
    return () => observer.disconnect()
  }, [target, onIntersect, enabled])
}

// Usage with infinite query
function InfiniteList() {
  const loadMoreRef = useRef<HTMLDivElement>(null)
  const { fetchNextPage, hasNextPage, isFetchingNextPage } = useInfiniteBuildings({})

  useIntersectionObserver({
    target: loadMoreRef,
    onIntersect: fetchNextPage,
    enabled: hasNextPage && !isFetchingNextPage,
  })

  return (
    <>
      {/* ... list items ... */}
      <div ref={loadMoreRef} className="h-10" />
      {isFetchingNextPage && <Spinner />}
    </>
  )
}
```

## Dependent Queries

### 1. Sequential Data Loading

```typescript
function useBuildingWithLeases(buildingId: string) {
  // First query: get building
  const buildingQuery = useQuery({
    queryKey: buildingKeys.detail(buildingId),
    queryFn: () => fetchBuilding(buildingId),
  });

  // Second query: depends on building data
  const leasesQuery = useQuery({
    queryKey: buildingKeys.leases(buildingId),
    queryFn: () => fetchLeases(buildingId),
    // Only run when building is loaded
    enabled: !!buildingQuery.data,
  });

  return {
    building: buildingQuery.data,
    leases: leasesQuery.data,
    isLoading: buildingQuery.isLoading || leasesQuery.isLoading,
  };
}
```

### 2. Parallel Queries with useQueries

```typescript
function useBuildingsDetails(ids: string[]) {
  return useQueries({
    queries: ids.map((id) => ({
      queryKey: buildingKeys.detail(id),
      queryFn: () => fetchBuilding(id),
      staleTime: 5 * 60 * 1000,
    })),
    combine: (results) => ({
      data: results.map((r) => r.data).filter(Boolean),
      isLoading: results.some((r) => r.isLoading),
      isError: results.some((r) => r.isError),
    }),
  });
}
```

## Optimistic Updates

### 1. Immediate Feedback Pattern

```typescript
function useUpdateBuilding() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: updateBuilding,

    onMutate: async (newBuilding) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({
        queryKey: buildingKeys.detail(newBuilding.id),
      });

      // Snapshot previous value
      const previousBuilding = queryClient.getQueryData(
        buildingKeys.detail(newBuilding.id),
      );

      // Optimistically update
      queryClient.setQueryData(
        buildingKeys.detail(newBuilding.id),
        (old: Building) => ({ ...old, ...newBuilding }),
      );

      // Return context for rollback
      return { previousBuilding };
    },

    onError: (err, newBuilding, context) => {
      // Rollback on error
      if (context?.previousBuilding) {
        queryClient.setQueryData(
          buildingKeys.detail(newBuilding.id),
          context.previousBuilding,
        );
      }
    },

    onSettled: (data, error, variables) => {
      // Refetch to ensure sync
      queryClient.invalidateQueries({
        queryKey: buildingKeys.detail(variables.id),
      });
    },
  });
}
```

### 2. Optimistic Delete with List Update

```typescript
function useDeleteBuilding() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (id: string) => deleteBuilding(id),

    onMutate: async (deletedId) => {
      await queryClient.cancelQueries({ queryKey: buildingKeys.lists() });

      // Snapshot all list queries
      const previousLists = queryClient.getQueriesData({
        queryKey: buildingKeys.lists(),
      });

      // Optimistically remove from all lists
      queryClient.setQueriesData(
        { queryKey: buildingKeys.lists() },
        (old: Building[] | undefined) => old?.filter((b) => b.id !== deletedId),
      );

      return { previousLists };
    },

    onError: (err, deletedId, context) => {
      // Restore all lists
      context?.previousLists.forEach(([queryKey, data]) => {
        queryClient.setQueryData(queryKey, data);
      });
    },

    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: buildingKeys.lists() });
    },
  });
}
```

## Suspense Mode

### 1. Suspense Query

```typescript
function BuildingDetails({ id }: { id: string }) {
  // This will suspend until data is ready
  const { data: building } = useSuspenseQuery({
    queryKey: buildingKeys.detail(id),
    queryFn: () => fetchBuilding(id),
  })

  // No need to check loading state - data is guaranteed
  return (
    <div>
      <h1>{building.name}</h1>
      <p>{building.address}</p>
    </div>
  )
}

// Parent component
function BuildingPage({ id }) {
  return (
    <Suspense fallback={<BuildingSkeleton />}>
      <BuildingDetails id={id} />
    </Suspense>
  )
}
```

### 2. Error Boundary Integration

```typescript
import { ErrorBoundary } from 'react-error-boundary'

function BuildingPage({ id }) {
  return (
    <ErrorBoundary
      fallback={<ErrorState message="Failed to load building" />}
      onReset={() => {
        queryClient.invalidateQueries({ queryKey: buildingKeys.detail(id) })
      }}
    >
      <Suspense fallback={<BuildingSkeleton />}>
        <BuildingDetails id={id} />
      </Suspense>
    </ErrorBoundary>
  )
}
```

## Testing

### 1. Test Setup

```typescript
// test/setup.ts
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { render } from '@testing-library/react'

export function createTestQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
        gcTime: 0,
      },
    },
  })
}

export function renderWithClient(ui: React.ReactElement) {
  const queryClient = createTestQueryClient()
  return {
    ...render(
      <QueryClientProvider client={queryClient}>
        {ui}
      </QueryClientProvider>
    ),
    queryClient,
  }
}
```

### 2. Mocking with MSW

```typescript
import { setupServer } from 'msw/node'
import { http, HttpResponse } from 'msw'

const handlers = [
  http.get('/api/buildings/:id', ({ params }) => {
    return HttpResponse.json({
      id: params.id,
      name: 'Test Building',
      address: '123 Test St',
    })
  }),
]

const server = setupServer(...handlers)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

// Test
test('displays building details', async () => {
  const { getByText } = renderWithClient(<BuildingDetails id="123" />)

  await waitFor(() => {
    expect(getByText('Test Building')).toBeInTheDocument()
  })
})
```

### 3. Testing Mutations

```typescript
test('updates building and invalidates cache', async () => {
  const { queryClient } = renderWithClient(<BuildingEditor id="123" />)

  // Seed initial data
  queryClient.setQueryData(buildingKeys.detail('123'), {
    id: '123',
    name: 'Old Name',
  })

  // Trigger mutation
  const user = userEvent.setup()
  await user.type(screen.getByLabelText('Name'), 'New Name')
  await user.click(screen.getByRole('button', { name: 'Save' }))

  // Verify optimistic update
  await waitFor(() => {
    expect(queryClient.getQueryData(buildingKeys.detail('123'))).toMatchObject({
      name: 'New Name',
    })
  })
})
```

## Best Practices

### Do's

- **Use query key factories** for consistent cache management
- **Prefetch on hover** for perceived performance
- **Use `staleTime`** to reduce unnecessary fetches
- **Combine results** with `combine` option in `useQueries`
- **Use Suspense** for cleaner loading states

### Don'ts

- **Don't forget error boundaries** with Suspense
- **Don't skip rollback logic** in optimistic updates
- **Don't use `cacheTime: 0`** in production (renamed to `gcTime`)
- **Don't mutate query data directly** - use `queryClient.setQueryData`

## Resources

- [TanStack Query Documentation](https://tanstack.com/query/latest)
- [Practical React Query](https://tkdodo.eu/blog/practical-react-query)
- [Testing React Query](https://tanstack.com/query/latest/docs/react/guides/testing)
