---
name: shadcn-ui-patterns
description: Master shadcn/ui component patterns, customization, theming, and composition. Use when building accessible React UIs with shadcn/ui primitives.
---

# shadcn/ui Patterns

Comprehensive guide to building beautiful, accessible React UIs with shadcn/ui components.

## Use this skill when

- Adding shadcn/ui components to a project
- Customizing component styling and behavior
- Building complex components from primitives
- Implementing forms with react-hook-form
- Creating data tables with TanStack Table
- Setting up theming and dark mode

## Do not use this skill when

- Using other component libraries (MUI, Chakra, Ant Design)
- Building native mobile apps
- Working with non-React frameworks

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.

## Getting Started

### 1. Installation

```bash
# Initialize shadcn/ui in your project
npx shadcn@latest init

# Configuration prompts:
# - TypeScript: Yes
# - Style: Default or New York
# - Base color: Slate, Gray, Zinc, Neutral, or Stone
# - CSS variables: Yes (recommended)
# - Tailwind CSS: Yes
# - Components location: @/components
# - Utils location: @/lib/utils

# Add individual components
npx shadcn@latest add button
npx shadcn@latest add card dialog form input
```

### 2. Project Structure

```
src/
├── components/
│   ├── ui/              # shadcn/ui components (auto-generated)
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   └── ...
│   └── patterns/        # Custom composite patterns
│       ├── page-header.tsx
│       └── data-table.tsx
├── lib/
│   └── utils.ts         # cn() utility
└── styles/
    └── globals.css      # CSS variables
```

## Core Patterns

### 1. Button Variants

```tsx
import { Button } from '@/components/ui/button'

// All variants
<Button variant="default">Default</Button>
<Button variant="destructive">Delete</Button>
<Button variant="outline">Outline</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>

// Sizes
<Button size="default">Default</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon"><Icon /></Button>

// With loading state
function SubmitButton() {
  const [loading, setLoading] = useState(false)

  return (
    <Button disabled={loading} onClick={() => setLoading(true)}>
      {loading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
      {loading ? 'Saving...' : 'Save'}
    </Button>
  )
}
```

### 2. Card Composition

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

function PropertyCard({ property }) {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{property.name}</CardTitle>
        <CardDescription>{property.address}</CardDescription>
      </CardHeader>
      <CardContent>
        <div className="grid gap-2">
          <div className="flex justify-between">
            <span className="text-muted-foreground">Type</span>
            <span>{property.type}</span>
          </div>
          <div className="flex justify-between">
            <span className="text-muted-foreground">Area</span>
            <span>{property.sqft.toLocaleString()} sqft</span>
          </div>
        </div>
      </CardContent>
      <CardFooter className="flex justify-between">
        <Button variant="outline">View Details</Button>
        <Button>Contact</Button>
      </CardFooter>
    </Card>
  );
}
```

### 3. Dialog Pattern

```tsx
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from "@/components/ui/dialog";

function ConfirmDeleteDialog({ onConfirm }) {
  const [open, setOpen] = useState(false);

  const handleConfirm = () => {
    onConfirm();
    setOpen(false);
  };

  return (
    <Dialog open={open} onOpenChange={setOpen}>
      <DialogTrigger asChild>
        <Button variant="destructive">Delete</Button>
      </DialogTrigger>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Are you sure?</DialogTitle>
          <DialogDescription>
            This action cannot be undone. This will permanently delete the
            record from our servers.
          </DialogDescription>
        </DialogHeader>
        <DialogFooter>
          <Button variant="outline" onClick={() => setOpen(false)}>
            Cancel
          </Button>
          <Button variant="destructive" onClick={handleConfirm}>
            Delete
          </Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  );
}
```

## Forms with react-hook-form

### 1. Form Setup

```bash
npx shadcn@latest add form input select textarea
npm install zod
```

### 2. Complete Form Example

```tsx
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";

const buildingSchema = z.object({
  name: z.string().min(2, "Name must be at least 2 characters"),
  address: z.string().min(5, "Address is required"),
  type: z.enum(["office", "retail", "industrial", "residential"]),
  description: z.string().optional(),
});

type BuildingFormValues = z.infer<typeof buildingSchema>;

function BuildingForm({ onSubmit, defaultValues }) {
  const form = useForm<BuildingFormValues>({
    resolver: zodResolver(buildingSchema),
    defaultValues: defaultValues ?? {
      name: "",
      address: "",
      type: "office",
      description: "",
    },
  });

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-6">
        <FormField
          control={form.control}
          name="name"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Building Name</FormLabel>
              <FormControl>
                <Input placeholder="Enter building name" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="address"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Address</FormLabel>
              <FormControl>
                <Input placeholder="123 Main St" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="type"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Building Type</FormLabel>
              <Select onValueChange={field.onChange} defaultValue={field.value}>
                <FormControl>
                  <SelectTrigger>
                    <SelectValue placeholder="Select a type" />
                  </SelectTrigger>
                </FormControl>
                <SelectContent>
                  <SelectItem value="office">Office</SelectItem>
                  <SelectItem value="retail">Retail</SelectItem>
                  <SelectItem value="industrial">Industrial</SelectItem>
                  <SelectItem value="residential">Residential</SelectItem>
                </SelectContent>
              </Select>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="description"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Description</FormLabel>
              <FormControl>
                <Textarea
                  placeholder="Optional description..."
                  className="resize-none"
                  {...field}
                />
              </FormControl>
              <FormDescription>
                Add any additional details about the building.
              </FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />

        <Button type="submit" disabled={form.formState.isSubmitting}>
          {form.formState.isSubmitting ? "Saving..." : "Save Building"}
        </Button>
      </form>
    </Form>
  );
}
```

## Data Tables

### 1. Setup

```bash
npx shadcn@latest add table
npm install @tanstack/react-table
```

### 2. Sortable, Filterable Table

```tsx
import {
  ColumnDef,
  flexRender,
  getCoreRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  useReactTable,
  SortingState,
} from "@tanstack/react-table";
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { ArrowUpDown } from "lucide-react";

interface Building {
  id: string;
  name: string;
  address: string;
  sqft: number;
  status: "active" | "inactive";
}

const columns: ColumnDef<Building>[] = [
  {
    accessorKey: "name",
    header: ({ column }) => (
      <Button
        variant="ghost"
        onClick={() => column.toggleSorting(column.getIsSorted() === "asc")}
      >
        Name
        <ArrowUpDown className="ml-2 h-4 w-4" />
      </Button>
    ),
  },
  {
    accessorKey: "address",
    header: "Address",
  },
  {
    accessorKey: "sqft",
    header: "Area (sqft)",
    cell: ({ row }) => row.original.sqft.toLocaleString(),
  },
  {
    accessorKey: "status",
    header: "Status",
    cell: ({ row }) => (
      <Badge
        variant={row.original.status === "active" ? "default" : "secondary"}
      >
        {row.original.status}
      </Badge>
    ),
  },
];

function BuildingsTable({ data }: { data: Building[] }) {
  const [sorting, setSorting] = useState<SortingState>([]);
  const [globalFilter, setGlobalFilter] = useState("");

  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
    onSortingChange: setSorting,
    onGlobalFilterChange: setGlobalFilter,
    state: { sorting, globalFilter },
  });

  return (
    <div className="space-y-4">
      <Input
        placeholder="Search buildings..."
        value={globalFilter}
        onChange={(e) => setGlobalFilter(e.target.value)}
        className="max-w-sm"
      />

      <div className="rounded-md border">
        <Table>
          <TableHeader>
            {table.getHeaderGroups().map((headerGroup) => (
              <TableRow key={headerGroup.id}>
                {headerGroup.headers.map((header) => (
                  <TableHead key={header.id}>
                    {header.isPlaceholder
                      ? null
                      : flexRender(
                          header.column.columnDef.header,
                          header.getContext(),
                        )}
                  </TableHead>
                ))}
              </TableRow>
            ))}
          </TableHeader>
          <TableBody>
            {table.getRowModel().rows?.length ? (
              table.getRowModel().rows.map((row) => (
                <TableRow key={row.id}>
                  {row.getVisibleCells().map((cell) => (
                    <TableCell key={cell.id}>
                      {flexRender(
                        cell.column.columnDef.cell,
                        cell.getContext(),
                      )}
                    </TableCell>
                  ))}
                </TableRow>
              ))
            ) : (
              <TableRow>
                <TableCell
                  colSpan={columns.length}
                  className="h-24 text-center"
                >
                  No results.
                </TableCell>
              </TableRow>
            )}
          </TableBody>
        </Table>
      </div>

      <div className="flex items-center justify-end space-x-2">
        <Button
          variant="outline"
          size="sm"
          onClick={() => table.previousPage()}
          disabled={!table.getCanPreviousPage()}
        >
          Previous
        </Button>
        <Button
          variant="outline"
          size="sm"
          onClick={() => table.nextPage()}
          disabled={!table.getCanNextPage()}
        >
          Next
        </Button>
      </div>
    </div>
  );
}
```

## Theming

### 1. CSS Variables

```css
/* globals.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    /* ... dark mode values */
  }
}
```

### 2. Dark Mode Toggle

```tsx
import { Moon, Sun } from "lucide-react";
import { useTheme } from "next-themes";
import { Button } from "@/components/ui/button";
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu";

export function ThemeToggle() {
  const { setTheme } = useTheme();

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="outline" size="icon">
          <Sun className="h-4 w-4 rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-4 w-4 rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">Toggle theme</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme("light")}>
          Light
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          Dark
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("system")}>
          System
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  );
}
```

## Custom Components

### 1. Page Header Pattern

```tsx
// components/patterns/page-header.tsx
interface PageHeaderProps {
  title: string;
  description?: string;
  actions?: React.ReactNode;
}

export function PageHeader({ title, description, actions }: PageHeaderProps) {
  return (
    <div className="flex items-center justify-between pb-6">
      <div className="space-y-1">
        <h1 className="text-2xl font-bold tracking-tight">{title}</h1>
        {description && <p className="text-muted-foreground">{description}</p>}
      </div>
      {actions && <div className="flex items-center gap-2">{actions}</div>}
    </div>
  );
}

// Usage
<PageHeader
  title="Buildings"
  description="Manage your property portfolio"
  actions={
    <>
      <Button variant="outline">Export</Button>
      <Button>Add Building</Button>
    </>
  }
/>;
```

### 2. Empty State Pattern

```tsx
// components/patterns/empty-state.tsx
interface EmptyStateProps {
  icon?: React.ReactNode;
  title: string;
  description?: string;
  action?: React.ReactNode;
}

export function EmptyState({
  icon,
  title,
  description,
  action,
}: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12 text-center">
      {icon && <div className="mb-4 rounded-full bg-muted p-3">{icon}</div>}
      <h3 className="text-lg font-semibold">{title}</h3>
      {description && (
        <p className="mt-1 text-sm text-muted-foreground max-w-sm">
          {description}
        </p>
      )}
      {action && <div className="mt-4">{action}</div>}
    </div>
  );
}

// Usage
<EmptyState
  icon={<Building2 className="h-6 w-6" />}
  title="No buildings yet"
  description="Get started by adding your first property to the portfolio."
  action={<Button>Add Building</Button>}
/>;
```

## Best Practices

### Do's

- **Use `asChild`** to compose with your own elements
- **Copy components** to customize - they're yours to modify
- **Use semantic colors** via CSS variables
- **Add `sr-only` labels** for accessibility
- **Compose from primitives** rather than prop-drilling

### Don'ts

- **Don't override with !important** - use CSS variable overrides
- **Don't skip form validation** - always use zod schemas
- **Don't forget focus states** - they're built-in, keep them
- **Don't nest interactive elements** - causes accessibility issues

## Resources

- [shadcn/ui Documentation](https://ui.shadcn.com)
- [Radix UI Primitives](https://www.radix-ui.com)
- [react-hook-form](https://react-hook-form.com)
- [TanStack Table](https://tanstack.com/table)
