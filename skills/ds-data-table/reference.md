# DataTable Advanced Reference

Advanced patterns for TanStack Table. For basic setup (sorting, filtering, pagination, selection), see [SKILL.md](./SKILL.md).

## Table of Contents

1. [Server-Side Data Handling](#1-server-side-data-handling)
2. [Row Expansion](#2-row-expansion)
3. [Cell Editing](#3-cell-editing)
4. [Column Management](#4-column-management)
5. [Virtual Scrolling Integration](#5-virtual-scrolling-integration)
6. [Advanced Selection](#6-advanced-selection)
7. [Accessibility Patterns](#7-accessibility-patterns)
8. [Performance Optimization](#8-performance-optimization)

---

## 1. Server-Side Data Handling

When datasets exceed what can be loaded client-side (10,000+ rows), move sorting, filtering, and pagination to the server.

### Server-Side Pagination

```typescript
// src/components/ui/data-table/server-data-table.tsx
import { useState } from 'react'
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  type ColumnDef,
  type PaginationState,
  type SortingState,
  type ColumnFiltersState,
} from '@tanstack/react-table'
import { useQuery } from '@tanstack/react-query'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'
import { Button } from '@/components/ui/button'
import { Skeleton } from '@/components/ui/skeleton'

interface ServerDataTableProps<TData, TValue> {
  columns: ColumnDef<TData, TValue>[]
  fetchFn: (params: {
    pagination: PaginationState
    sorting: SortingState
    filters: ColumnFiltersState
  }) => Promise<{ data: TData[]; pageCount: number; totalCount: number }>
  queryKey: string[]
}

export function ServerDataTable<TData, TValue>({
  columns,
  fetchFn,
  queryKey,
}: ServerDataTableProps<TData, TValue>) {
  const [pagination, setPagination] = useState<PaginationState>({
    pageIndex: 0,
    pageSize: 20,
  })
  const [sorting, setSorting] = useState<SortingState>([])
  const [columnFilters, setColumnFilters] = useState<ColumnFiltersState>([])

  const { data, isLoading, isFetching } = useQuery({
    queryKey: [...queryKey, pagination, sorting, columnFilters],
    queryFn: () => fetchFn({ pagination, sorting, filters: columnFilters }),
    placeholderData: (prev) => prev, // Keep previous data while fetching
    staleTime: 30_000, // 30 seconds
  })

  const table = useReactTable({
    data: data?.data ?? [],
    columns,
    pageCount: data?.pageCount ?? -1,
    state: { pagination, sorting, columnFilters },
    onPaginationChange: setPagination,
    onSortingChange: setSorting,
    onColumnFiltersChange: setColumnFilters,
    getCoreRowModel: getCoreRowModel(),
    manualPagination: true,
    manualSorting: true,
    manualFiltering: true,
  })

  return (
    <div className="space-y-4">
      {/* Loading indicator for refetch */}
      {isFetching && !isLoading && (
        <div className="absolute right-4 top-4">
          <div className="h-4 w-4 animate-spin rounded-full border-2 border-primary border-t-transparent" />
        </div>
      )}

      <div className="relative rounded-md border">
        <Table>
          <TableHeader>
            {table.getHeaderGroups().map((headerGroup) => (
              <TableRow key={headerGroup.id}>
                {headerGroup.headers.map((header) => (
                  <TableHead key={header.id}>
                    {header.isPlaceholder
                      ? null
                      : flexRender(header.column.columnDef.header, header.getContext())}
                  </TableHead>
                ))}
              </TableRow>
            ))}
          </TableHeader>
          <TableBody>
            {isLoading ? (
              // Skeleton rows during initial load
              Array.from({ length: pagination.pageSize }).map((_, i) => (
                <TableRow key={i}>
                  {columns.map((_, j) => (
                    <TableCell key={j}>
                      <Skeleton className="h-4 w-full" />
                    </TableCell>
                  ))}
                </TableRow>
              ))
            ) : table.getRowModel().rows.length ? (
              table.getRowModel().rows.map((row) => (
                <TableRow key={row.id}>
                  {row.getVisibleCells().map((cell) => (
                    <TableCell key={cell.id}>
                      {flexRender(cell.column.columnDef.cell, cell.getContext())}
                    </TableCell>
                  ))}
                </TableRow>
              ))
            ) : (
              <TableRow>
                <TableCell colSpan={columns.length} className="h-24 text-center">
                  No results.
                </TableCell>
              </TableRow>
            )}
          </TableBody>
        </Table>
      </div>

      {/* Pagination */}
      <div className="flex items-center justify-between">
        <p className="text-sm text-muted-foreground">
          Showing {pagination.pageIndex * pagination.pageSize + 1} to{' '}
          {Math.min((pagination.pageIndex + 1) * pagination.pageSize, data?.totalCount ?? 0)} of{' '}
          {data?.totalCount ?? 0} results
        </p>
        <div className="flex items-center gap-2">
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.previousPage()}
            disabled={!table.getCanPreviousPage()}
          >
            Previous
          </Button>
          <span className="text-sm text-muted-foreground">
            Page {pagination.pageIndex + 1} of {table.getPageCount()}
          </span>
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
    </div>
  )
}
```

### Server-Side API Handler

```typescript
// src/app/api/users/route.ts (Next.js Route Handler)
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/lib/db'

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams

  const pageIndex = parseInt(searchParams.get('pageIndex') ?? '0')
  const pageSize = parseInt(searchParams.get('pageSize') ?? '20')
  const sortField = searchParams.get('sortField')
  const sortDir = searchParams.get('sortDir') as 'asc' | 'desc' | null
  const filters = JSON.parse(searchParams.get('filters') ?? '[]')

  // Build query with filters
  let query = db.selectFrom('users')

  for (const filter of filters) {
    if (filter.id === 'email') {
      query = query.where('email', 'ilike', `%${filter.value}%`)
    }
    if (filter.id === 'role') {
      query = query.where('role', '=', filter.value)
    }
  }

  // Get total count
  const totalCount = await query.clone().count().executeTakeFirst()

  // Apply sorting
  if (sortField && sortDir) {
    query = query.orderBy(sortField, sortDir)
  }

  // Apply pagination
  const data = await query
    .offset(pageIndex * pageSize)
    .limit(pageSize)
    .selectAll()
    .execute()

  return NextResponse.json({
    data,
    pageCount: Math.ceil(Number(totalCount?.count ?? 0) / pageSize),
    totalCount: Number(totalCount?.count ?? 0),
  })
}
```

### Optimistic Updates

```typescript
// src/hooks/use-optimistic-mutation.ts
import { useMutation, useQueryClient } from '@tanstack/react-query'

interface User {
  id: string
  name: string
  email: string
  role: 'admin' | 'user'
}

export function useUpdateUser() {
  const queryClient = useQueryClient()

  return useMutation({
    mutationFn: async (user: Partial<User> & { id: string }) => {
      const res = await fetch(`/api/users/${user.id}`, {
        method: 'PATCH',
        body: JSON.stringify(user),
      })
      return res.json()
    },
    onMutate: async (newUser) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: ['users'] })

      // Snapshot previous value
      const previousData = queryClient.getQueryData(['users'])

      // Optimistically update
      queryClient.setQueriesData({ queryKey: ['users'] }, (old: any) => {
        if (!old?.data) return old
        return {
          ...old,
          data: old.data.map((user: User) =>
            user.id === newUser.id ? { ...user, ...newUser } : user
          ),
        }
      })

      return { previousData }
    },
    onError: (_err, _newUser, context) => {
      // Rollback on error
      if (context?.previousData) {
        queryClient.setQueryData(['users'], context.previousData)
      }
    },
    onSettled: () => {
      // Refetch after mutation
      queryClient.invalidateQueries({ queryKey: ['users'] })
    },
  })
}
```

### Debounced Server Filtering

```typescript
// src/components/ui/data-table/debounced-input.tsx
import { useState, useEffect } from 'react'
import { Input } from '@/components/ui/input'

interface DebouncedInputProps {
  value: string
  onChange: (value: string) => void
  debounceMs?: number
  placeholder?: string
}

export function DebouncedInput({
  value: initialValue,
  onChange,
  debounceMs = 300,
  placeholder,
}: DebouncedInputProps) {
  const [value, setValue] = useState(initialValue)

  useEffect(() => {
    setValue(initialValue)
  }, [initialValue])

  useEffect(() => {
    const timeout = setTimeout(() => {
      if (value !== initialValue) {
        onChange(value)
      }
    }, debounceMs)

    return () => clearTimeout(timeout)
  }, [value, debounceMs, onChange, initialValue])

  return (
    <Input
      value={value}
      onChange={(e) => setValue(e.target.value)}
      placeholder={placeholder}
      className="max-w-sm"
    />
  )
}
```

---

## 2. Row Expansion

Enable expandable rows for hierarchical data or detail views.

### Expandable Rows Pattern

```typescript
// src/components/ui/data-table/expandable-table.tsx
import { useState, Fragment } from 'react'
import {
  useReactTable,
  getCoreRowModel,
  getExpandedRowModel,
  flexRender,
  type ColumnDef,
  type ExpandedState,
  type Row,
} from '@tanstack/react-table'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'
import { Button } from '@/components/ui/button'
import { ChevronRight, ChevronDown } from 'lucide-react'

interface ExpandableTableProps<TData, TValue> {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
  renderExpandedRow: (row: Row<TData>) => React.ReactNode
  getRowCanExpand?: (row: Row<TData>) => boolean
}

export function ExpandableTable<TData, TValue>({
  columns,
  data,
  renderExpandedRow,
  getRowCanExpand = () => true,
}: ExpandableTableProps<TData, TValue>) {
  const [expanded, setExpanded] = useState<ExpandedState>({})

  const expanderColumn: ColumnDef<TData, TValue> = {
    id: 'expander',
    header: () => null,
    cell: ({ row }) => {
      if (!row.getCanExpand()) return null

      return (
        <Button
          variant="ghost"
          size="sm"
          onClick={row.getToggleExpandedHandler()}
          aria-label={row.getIsExpanded() ? 'Collapse row' : 'Expand row'}
          aria-expanded={row.getIsExpanded()}
        >
          {row.getIsExpanded() ? (
            <ChevronDown className="h-4 w-4" />
          ) : (
            <ChevronRight className="h-4 w-4" />
          )}
        </Button>
      )
    },
  }

  const table = useReactTable({
    data,
    columns: [expanderColumn, ...columns],
    state: { expanded },
    onExpandedChange: setExpanded,
    getRowCanExpand,
    getCoreRowModel: getCoreRowModel(),
    getExpandedRowModel: getExpandedRowModel(),
  })

  return (
    <div className="rounded-md border">
      <Table>
        <TableHeader>
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map((header) => (
                <TableHead key={header.id}>
                  {header.isPlaceholder
                    ? null
                    : flexRender(header.column.columnDef.header, header.getContext())}
                </TableHead>
              ))}
            </TableRow>
          ))}
        </TableHeader>
        <TableBody>
          {table.getRowModel().rows.map((row) => (
            <Fragment key={row.id}>
              <TableRow data-state={row.getIsExpanded() && 'expanded'}>
                {row.getVisibleCells().map((cell) => (
                  <TableCell key={cell.id}>
                    {flexRender(cell.column.columnDef.cell, cell.getContext())}
                  </TableCell>
                ))}
              </TableRow>
              {row.getIsExpanded() && (
                <TableRow className="bg-muted/50 hover:bg-muted/50">
                  <TableCell colSpan={row.getVisibleCells().length} className="p-0">
                    {renderExpandedRow(row)}
                  </TableCell>
                </TableRow>
              )}
            </Fragment>
          ))}
        </TableBody>
      </Table>
    </div>
  )
}
```

### Hierarchical Data (Sub-rows)

```typescript
// src/components/features/category-table.tsx
import { useMemo } from 'react'
import {
  useReactTable,
  getCoreRowModel,
  getExpandedRowModel,
  flexRender,
  type ColumnDef,
} from '@tanstack/react-table'

interface Category {
  id: string
  name: string
  itemCount: number
  subCategories?: Category[]
}

const columns: ColumnDef<Category>[] = [
  {
    id: 'expander',
    header: () => null,
    cell: ({ row }) => {
      if (!row.getCanExpand()) return <span className="w-8 inline-block" />

      return (
        <Button
          variant="ghost"
          size="sm"
          onClick={row.getToggleExpandedHandler()}
          style={{ marginLeft: `${row.depth * 24}px` }}
        >
          {row.getIsExpanded() ? (
            <ChevronDown className="h-4 w-4" />
          ) : (
            <ChevronRight className="h-4 w-4" />
          )}
        </Button>
      )
    },
  },
  {
    accessorKey: 'name',
    header: 'Category',
  },
  {
    accessorKey: 'itemCount',
    header: 'Items',
  },
]

export function CategoryTable({ data }: { data: Category[] }) {
  const table = useReactTable({
    data,
    columns,
    getSubRows: (row) => row.subCategories,
    getCoreRowModel: getCoreRowModel(),
    getExpandedRowModel: getExpandedRowModel(),
  })

  // ... render table
}
```

### Async Content Loading in Expanded Rows

```typescript
// src/components/features/order-details.tsx
import { useQuery } from '@tanstack/react-query'
import { Skeleton } from '@/components/ui/skeleton'
import type { Row } from '@tanstack/react-table'

interface Order {
  id: string
  customerName: string
  total: number
}

interface OrderDetails {
  items: Array<{ name: string; quantity: number; price: number }>
  shippingAddress: string
  notes: string
}

function OrderDetailsPanel({ row }: { row: Row<Order> }) {
  const { data, isLoading } = useQuery({
    queryKey: ['order-details', row.original.id],
    queryFn: () => fetchOrderDetails(row.original.id),
    // Only fetch when row is expanded
    enabled: row.getIsExpanded(),
  })

  if (isLoading) {
    return (
      <div className="p-6 space-y-4">
        <Skeleton className="h-4 w-48" />
        <Skeleton className="h-4 w-64" />
        <Skeleton className="h-4 w-32" />
      </div>
    )
  }

  if (!data) return null

  return (
    <div className="p-6 space-y-4">
      <div>
        <h4 className="font-medium mb-2">Items</h4>
        <ul className="space-y-1">
          {data.items.map((item, i) => (
            <li key={i} className="text-sm text-muted-foreground">
              {item.quantity}x {item.name} - ${item.price}
            </li>
          ))}
        </ul>
      </div>
      <div>
        <h4 className="font-medium mb-1">Shipping Address</h4>
        <p className="text-sm text-muted-foreground">{data.shippingAddress}</p>
      </div>
      {data.notes && (
        <div>
          <h4 className="font-medium mb-1">Notes</h4>
          <p className="text-sm text-muted-foreground">{data.notes}</p>
        </div>
      )}
    </div>
  )
}

// Usage
<ExpandableTable
  columns={orderColumns}
  data={orders}
  renderExpandedRow={(row) => <OrderDetailsPanel row={row} />}
  getRowCanExpand={() => true}
/>
```

---

## 3. Cell Editing

Enable inline editing for data tables.

### Inline Editing Pattern

```typescript
// src/components/ui/data-table/editable-cell.tsx
import { useState, useEffect, useCallback } from 'react'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Check, X } from 'lucide-react'
import type { CellContext } from '@tanstack/react-table'

interface EditableCellProps<TData, TValue> {
  getValue: () => TValue
  row: CellContext<TData, TValue>['row']
  column: CellContext<TData, TValue>['column']
  table: CellContext<TData, TValue>['table']
  onSave: (rowId: string, columnId: string, value: TValue) => Promise<void>
  validate?: (value: TValue) => string | null
}

export function EditableCell<TData, TValue extends string | number>({
  getValue,
  row,
  column,
  table,
  onSave,
  validate,
}: EditableCellProps<TData, TValue>) {
  const initialValue = getValue()
  const [value, setValue] = useState<TValue>(initialValue)
  const [isEditing, setIsEditing] = useState(false)
  const [error, setError] = useState<string | null>(null)
  const [isSaving, setIsSaving] = useState(false)

  // Reset value when row data changes
  useEffect(() => {
    setValue(initialValue)
  }, [initialValue])

  const handleSave = useCallback(async () => {
    if (validate) {
      const validationError = validate(value)
      if (validationError) {
        setError(validationError)
        return
      }
    }

    setIsSaving(true)
    try {
      await onSave(row.id, column.id, value)
      setIsEditing(false)
      setError(null)
    } catch (e) {
      setError('Failed to save')
    } finally {
      setIsSaving(false)
    }
  }, [value, row.id, column.id, onSave, validate])

  const handleCancel = useCallback(() => {
    setValue(initialValue)
    setIsEditing(false)
    setError(null)
  }, [initialValue])

  const handleKeyDown = useCallback(
    (e: React.KeyboardEvent) => {
      if (e.key === 'Enter') {
        handleSave()
      } else if (e.key === 'Escape') {
        handleCancel()
      }
    },
    [handleSave, handleCancel]
  )

  if (isEditing) {
    return (
      <div className="flex items-center gap-1">
        <div className="flex-1">
          <Input
            value={value}
            onChange={(e) => setValue(e.target.value as TValue)}
            onKeyDown={handleKeyDown}
            autoFocus
            className={error ? 'border-destructive' : ''}
            disabled={isSaving}
          />
          {error && (
            <p className="text-xs text-destructive mt-1">{error}</p>
          )}
        </div>
        <Button
          variant="ghost"
          size="icon"
          onClick={handleSave}
          disabled={isSaving}
          aria-label="Save"
        >
          <Check className="h-4 w-4 text-primary" />
        </Button>
        <Button
          variant="ghost"
          size="icon"
          onClick={handleCancel}
          disabled={isSaving}
          aria-label="Cancel"
        >
          <X className="h-4 w-4 text-muted-foreground" />
        </Button>
      </div>
    )
  }

  return (
    <span
      onClick={() => setIsEditing(true)}
      onKeyDown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          setIsEditing(true)
        }
      }}
      tabIndex={0}
      role="button"
      className="cursor-pointer hover:bg-muted px-2 py-1 rounded -mx-2 -my-1 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring"
      aria-label={`Edit ${column.id}: ${value}`}
    >
      {String(value)}
    </span>
  )
}
```

### Using Editable Cells in Columns

```typescript
// src/app/products/columns.tsx
import type { ColumnDef } from '@tanstack/react-table'
import { EditableCell } from '@/components/ui/data-table/editable-cell'

interface Product {
  id: string
  name: string
  price: number
  stock: number
}

const handleSave = async (rowId: string, columnId: string, value: string | number) => {
  await fetch(`/api/products/${rowId}`, {
    method: 'PATCH',
    body: JSON.stringify({ [columnId]: value }),
  })
}

export const columns: ColumnDef<Product>[] = [
  {
    accessorKey: 'name',
    header: 'Product Name',
    cell: (props) => (
      <EditableCell
        {...props}
        onSave={handleSave}
        validate={(value) => {
          if (!value || value.length < 2) {
            return 'Name must be at least 2 characters'
          }
          return null
        }}
      />
    ),
  },
  {
    accessorKey: 'price',
    header: 'Price',
    cell: (props) => (
      <EditableCell
        {...props}
        onSave={handleSave}
        validate={(value) => {
          const num = Number(value)
          if (isNaN(num) || num < 0) {
            return 'Price must be a positive number'
          }
          return null
        }}
      />
    ),
  },
  {
    accessorKey: 'stock',
    header: 'Stock',
  },
]
```

### Form-Based Editing (Modal)

```typescript
// src/components/features/edit-row-dialog.tsx
import { useState } from 'react'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogFooter,
} from '@/components/ui/dialog'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import type { Row } from '@tanstack/react-table'

const schema = z.object({
  name: z.string().min(2, 'Name must be at least 2 characters'),
  email: z.string().email('Invalid email'),
  role: z.enum(['admin', 'user']),
})

type FormData = z.infer<typeof schema>

interface EditRowDialogProps<TData> {
  row: Row<TData> | null
  onClose: () => void
  onSave: (data: FormData) => Promise<void>
}

export function EditRowDialog<TData extends FormData>({
  row,
  onClose,
  onSave,
}: EditRowDialogProps<TData>) {
  const [isSubmitting, setIsSubmitting] = useState(false)

  const {
    register,
    handleSubmit,
    formState: { errors },
    reset,
  } = useForm<FormData>({
    resolver: zodResolver(schema),
    values: row?.original,
  })

  const onSubmit = async (data: FormData) => {
    setIsSubmitting(true)
    try {
      await onSave(data)
      onClose()
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <Dialog open={!!row} onOpenChange={(open) => !open && onClose()}>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Edit User</DialogTitle>
        </DialogHeader>
        <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
          <div className="space-y-2">
            <Label htmlFor="name">Name</Label>
            <Input id="name" {...register('name')} />
            {errors.name && (
              <p className="text-sm text-destructive">{errors.name.message}</p>
            )}
          </div>
          <div className="space-y-2">
            <Label htmlFor="email">Email</Label>
            <Input id="email" type="email" {...register('email')} />
            {errors.email && (
              <p className="text-sm text-destructive">{errors.email.message}</p>
            )}
          </div>
          <DialogFooter>
            <Button type="button" variant="outline" onClick={onClose}>
              Cancel
            </Button>
            <Button type="submit" disabled={isSubmitting}>
              {isSubmitting ? 'Saving...' : 'Save Changes'}
            </Button>
          </DialogFooter>
        </form>
      </DialogContent>
    </Dialog>
  )
}
```

---

## 4. Column Management

Advanced column controls: pinning, resizing, reordering, and persistence.

### Column Pinning

```typescript
// src/components/ui/data-table/pinned-columns-table.tsx
import { useState } from 'react'
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  type ColumnDef,
  type ColumnPinningState,
} from '@tanstack/react-table'
import { cn } from '@/lib/utils'

interface PinnedColumnsTableProps<TData, TValue> {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
  defaultPinning?: ColumnPinningState
}

export function PinnedColumnsTable<TData, TValue>({
  columns,
  data,
  defaultPinning = { left: ['select'], right: ['actions'] },
}: PinnedColumnsTableProps<TData, TValue>) {
  const [columnPinning, setColumnPinning] = useState<ColumnPinningState>(defaultPinning)

  const table = useReactTable({
    data,
    columns,
    state: { columnPinning },
    onColumnPinningChange: setColumnPinning,
    getCoreRowModel: getCoreRowModel(),
  })

  const getCommonPinningStyles = (column: any): React.CSSProperties => {
    const isPinned = column.getIsPinned()
    const isLastLeftPinned =
      isPinned === 'left' && column.getIsLastColumn('left')
    const isFirstRightPinned =
      isPinned === 'right' && column.getIsFirstColumn('right')

    return {
      boxShadow: isLastLeftPinned
        ? '-4px 0 4px -4px gray inset'
        : isFirstRightPinned
          ? '4px 0 4px -4px gray inset'
          : undefined,
      left: isPinned === 'left' ? `${column.getStart('left')}px` : undefined,
      right: isPinned === 'right' ? `${column.getAfter('right')}px` : undefined,
      position: isPinned ? 'sticky' : 'relative',
      width: column.getSize(),
      zIndex: isPinned ? 1 : 0,
    }
  }

  return (
    <div className="overflow-x-auto">
      <Table>
        <TableHeader>
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map((header) => (
                <TableHead
                  key={header.id}
                  style={getCommonPinningStyles(header.column)}
                  className={cn(
                    header.column.getIsPinned() && 'bg-background'
                  )}
                >
                  {flexRender(header.column.columnDef.header, header.getContext())}
                </TableHead>
              ))}
            </TableRow>
          ))}
        </TableHeader>
        <TableBody>
          {table.getRowModel().rows.map((row) => (
            <TableRow key={row.id}>
              {row.getVisibleCells().map((cell) => (
                <TableCell
                  key={cell.id}
                  style={getCommonPinningStyles(cell.column)}
                  className={cn(
                    cell.column.getIsPinned() && 'bg-background'
                  )}
                >
                  {flexRender(cell.column.columnDef.cell, cell.getContext())}
                </TableCell>
              ))}
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </div>
  )
}
```

### Column Resizing

```typescript
// src/components/ui/data-table/resizable-table.tsx
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  type ColumnDef,
  type ColumnSizingState,
} from '@tanstack/react-table'
import { useState } from 'react'

export function ResizableTable<TData, TValue>({
  columns,
  data,
}: {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
}) {
  const [columnSizing, setColumnSizing] = useState<ColumnSizingState>({})

  const table = useReactTable({
    data,
    columns,
    state: { columnSizing },
    onColumnSizingChange: setColumnSizing,
    getCoreRowModel: getCoreRowModel(),
    columnResizeMode: 'onChange',
    columnResizeDirection: 'ltr',
  })

  return (
    <div className="overflow-x-auto">
      <Table style={{ width: table.getCenterTotalSize() }}>
        <TableHeader>
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map((header) => (
                <TableHead
                  key={header.id}
                  style={{ width: header.getSize() }}
                  className="relative group"
                >
                  {flexRender(header.column.columnDef.header, header.getContext())}
                  {header.column.getCanResize() && (
                    <div
                      onMouseDown={header.getResizeHandler()}
                      onTouchStart={header.getResizeHandler()}
                      className={cn(
                        'absolute right-0 top-0 h-full w-1 cursor-col-resize',
                        'bg-transparent hover:bg-primary',
                        'transition-colors duration-150',
                        header.column.getIsResizing() && 'bg-primary'
                      )}
                    />
                  )}
                </TableHead>
              ))}
            </TableRow>
          ))}
        </TableHeader>
        {/* ... body */}
      </Table>
    </div>
  )
}
```

### Column Reordering (Drag and Drop)

```typescript
// src/components/ui/data-table/reorderable-table.tsx
import { useState } from 'react'
import {
  DndContext,
  closestCenter,
  KeyboardSensor,
  PointerSensor,
  useSensor,
  useSensors,
  type DragEndEvent,
} from '@dnd-kit/core'
import {
  arrayMove,
  SortableContext,
  horizontalListSortingStrategy,
  useSortable,
} from '@dnd-kit/sortable'
import { CSS } from '@dnd-kit/utilities'
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  type ColumnDef,
  type ColumnOrderState,
} from '@tanstack/react-table'
import { GripVertical } from 'lucide-react'

function DraggableHeader({ header }: { header: any }) {
  const {
    attributes,
    listeners,
    setNodeRef,
    transform,
    transition,
    isDragging,
  } = useSortable({ id: header.id })

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
    opacity: isDragging ? 0.5 : 1,
  }

  return (
    <TableHead ref={setNodeRef} style={style} {...attributes}>
      <div className="flex items-center gap-2">
        <span {...listeners} className="cursor-grab active:cursor-grabbing">
          <GripVertical className="h-4 w-4 text-muted-foreground" />
        </span>
        {flexRender(header.column.columnDef.header, header.getContext())}
      </div>
    </TableHead>
  )
}

export function ReorderableTable<TData, TValue>({
  columns,
  data,
}: {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
}) {
  const [columnOrder, setColumnOrder] = useState<ColumnOrderState>(
    columns.map((col) => (col as any).accessorKey ?? (col as any).id)
  )

  const sensors = useSensors(
    useSensor(PointerSensor),
    useSensor(KeyboardSensor)
  )

  const table = useReactTable({
    data,
    columns,
    state: { columnOrder },
    onColumnOrderChange: setColumnOrder,
    getCoreRowModel: getCoreRowModel(),
  })

  const handleDragEnd = (event: DragEndEvent) => {
    const { active, over } = event
    if (active.id !== over?.id) {
      setColumnOrder((order) => {
        const oldIndex = order.indexOf(active.id as string)
        const newIndex = order.indexOf(over?.id as string)
        return arrayMove(order, oldIndex, newIndex)
      })
    }
  }

  return (
    <DndContext
      sensors={sensors}
      collisionDetection={closestCenter}
      onDragEnd={handleDragEnd}
    >
      <Table>
        <TableHeader>
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              <SortableContext
                items={headerGroup.headers.map((h) => h.id)}
                strategy={horizontalListSortingStrategy}
              >
                {headerGroup.headers.map((header) => (
                  <DraggableHeader key={header.id} header={header} />
                ))}
              </SortableContext>
            </TableRow>
          ))}
        </TableHeader>
        {/* ... body */}
      </Table>
    </DndContext>
  )
}
```

### Persistent Column State

```typescript
// src/hooks/use-table-state.ts
import { useState, useEffect, useCallback } from 'react'
import type {
  ColumnOrderState,
  ColumnSizingState,
  ColumnPinningState,
  VisibilityState,
} from '@tanstack/react-table'

interface TableState {
  columnOrder: ColumnOrderState
  columnSizing: ColumnSizingState
  columnPinning: ColumnPinningState
  columnVisibility: VisibilityState
}

const DEFAULT_STATE: TableState = {
  columnOrder: [],
  columnSizing: {},
  columnPinning: { left: [], right: [] },
  columnVisibility: {},
}

export function useTableState(tableId: string) {
  const storageKey = `table-state-${tableId}`

  const [state, setState] = useState<TableState>(() => {
    if (typeof window === 'undefined') return DEFAULT_STATE

    try {
      const stored = localStorage.getItem(storageKey)
      return stored ? JSON.parse(stored) : DEFAULT_STATE
    } catch {
      return DEFAULT_STATE
    }
  })

  // Persist state changes
  useEffect(() => {
    try {
      localStorage.setItem(storageKey, JSON.stringify(state))
    } catch (e) {
      console.error('Failed to persist table state:', e)
    }
  }, [state, storageKey])

  const resetState = useCallback(() => {
    setState(DEFAULT_STATE)
    localStorage.removeItem(storageKey)
  }, [storageKey])

  return {
    ...state,
    setColumnOrder: (order: ColumnOrderState) =>
      setState((s) => ({ ...s, columnOrder: order })),
    setColumnSizing: (sizing: ColumnSizingState) =>
      setState((s) => ({ ...s, columnSizing: sizing })),
    setColumnPinning: (pinning: ColumnPinningState) =>
      setState((s) => ({ ...s, columnPinning: pinning })),
    setColumnVisibility: (visibility: VisibilityState) =>
      setState((s) => ({ ...s, columnVisibility: visibility })),
    resetState,
  }
}

// Usage
function MyTable() {
  const {
    columnOrder,
    columnSizing,
    columnVisibility,
    setColumnOrder,
    setColumnSizing,
    setColumnVisibility,
    resetState,
  } = useTableState('users-table')

  const table = useReactTable({
    data,
    columns,
    state: { columnOrder, columnSizing, columnVisibility },
    onColumnOrderChange: setColumnOrder,
    onColumnSizingChange: setColumnSizing,
    onColumnVisibilityChange: setColumnVisibility,
    // ...
  })

  return (
    <div>
      <Button variant="ghost" size="sm" onClick={resetState}>
        Reset Layout
      </Button>
      {/* table */}
    </div>
  )
}
```

---

## 5. Virtual Scrolling Integration

For tables with 1000+ rows, virtualization prevents DOM bloat.

### When to Use Virtualization

| Row Count | Recommendation |
|-----------|----------------|
| < 100 | Standard table |
| 100-500 | Standard with pagination |
| 500-1000 | Consider virtualization |
| 1000+ | Use virtualization |

### TanStack Virtual Integration

```typescript
// src/components/ui/data-table/virtual-table.tsx
import { useRef } from 'react'
import { useVirtualizer } from '@tanstack/react-virtual'
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  flexRender,
  type ColumnDef,
  type SortingState,
  type RowSelectionState,
} from '@tanstack/react-table'
import { useState } from 'react'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'

interface VirtualTableProps<TData, TValue> {
  columns: ColumnDef<TData, TValue>[]
  data: TData[]
  rowHeight?: number
  containerHeight?: number
}

export function VirtualTable<TData, TValue>({
  columns,
  data,
  rowHeight = 48,
  containerHeight = 600,
}: VirtualTableProps<TData, TValue>) {
  const [sorting, setSorting] = useState<SortingState>([])
  const [rowSelection, setRowSelection] = useState<RowSelectionState>({})

  const table = useReactTable({
    data,
    columns,
    state: { sorting, rowSelection },
    onSortingChange: setSorting,
    onRowSelectionChange: setRowSelection,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    getRowId: (row) => (row as any).id, // Stable row IDs for selection
  })

  const { rows } = table.getRowModel()

  const parentRef = useRef<HTMLDivElement>(null)

  const virtualizer = useVirtualizer({
    count: rows.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => rowHeight,
    overscan: 10,
  })

  const virtualRows = virtualizer.getVirtualItems()
  const totalSize = virtualizer.getTotalSize()

  const paddingTop = virtualRows.length > 0 ? virtualRows[0].start : 0
  const paddingBottom =
    virtualRows.length > 0
      ? totalSize - virtualRows[virtualRows.length - 1].end
      : 0

  return (
    <div className="rounded-md border">
      {/* Fixed header */}
      <Table>
        <TableHeader>
          {table.getHeaderGroups().map((headerGroup) => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map((header) => (
                <TableHead key={header.id} style={{ width: header.getSize() }}>
                  {flexRender(header.column.columnDef.header, header.getContext())}
                </TableHead>
              ))}
            </TableRow>
          ))}
        </TableHeader>
      </Table>

      {/* Scrollable body */}
      <div
        ref={parentRef}
        style={{ height: containerHeight, overflow: 'auto' }}
      >
        <Table>
          <TableBody>
            {paddingTop > 0 && (
              <tr>
                <td style={{ height: paddingTop }} />
              </tr>
            )}
            {virtualRows.map((virtualRow) => {
              const row = rows[virtualRow.index]
              return (
                <TableRow
                  key={row.id}
                  data-state={row.getIsSelected() && 'selected'}
                  style={{ height: rowHeight }}
                >
                  {row.getVisibleCells().map((cell) => (
                    <TableCell key={cell.id}>
                      {flexRender(cell.column.columnDef.cell, cell.getContext())}
                    </TableCell>
                  ))}
                </TableRow>
              )
            })}
            {paddingBottom > 0 && (
              <tr>
                <td style={{ height: paddingBottom }} />
              </tr>
            )}
          </TableBody>
        </Table>
      </div>
    </div>
  )
}
```

### Maintaining Selection with Virtualization

```typescript
// Key considerations for selection + virtualization

// 1. Use stable row IDs (not array indices)
const table = useReactTable({
  data,
  columns,
  getRowId: (row) => row.id, // Must be stable
  // ...
})

// 2. Selection state persists even when rows unmount
const [rowSelection, setRowSelection] = useState<RowSelectionState>({})
// rowSelection = { 'user-123': true, 'user-456': true }

// 3. Display selection count correctly
const selectedCount = Object.keys(rowSelection).length

// 4. Get selected rows from original data (not just visible)
const selectedRows = data.filter((row) => rowSelection[row.id])
```

---

## 6. Advanced Selection

Complex selection patterns for bulk operations.

### Multi-Select with Shift+Click

```typescript
// src/hooks/use-range-selection.ts
import { useCallback, useRef } from 'react'
import type { RowSelectionState, Row } from '@tanstack/react-table'

export function useRangeSelection<TData>(
  rows: Row<TData>[],
  rowSelection: RowSelectionState,
  setRowSelection: (selection: RowSelectionState) => void
) {
  const lastSelectedRef = useRef<number | null>(null)

  const handleRowClick = useCallback(
    (index: number, event: React.MouseEvent) => {
      const row = rows[index]

      if (event.shiftKey && lastSelectedRef.current !== null) {
        // Range selection
        const start = Math.min(lastSelectedRef.current, index)
        const end = Math.max(lastSelectedRef.current, index)

        const newSelection = { ...rowSelection }
        for (let i = start; i <= end; i++) {
          newSelection[rows[i].id] = true
        }
        setRowSelection(newSelection)
      } else if (event.metaKey || event.ctrlKey) {
        // Toggle single
        setRowSelection({
          ...rowSelection,
          [row.id]: !rowSelection[row.id],
        })
        lastSelectedRef.current = index
      } else {
        // Single select (clear others)
        setRowSelection({ [row.id]: true })
        lastSelectedRef.current = index
      }
    },
    [rows, rowSelection, setRowSelection]
  )

  return handleRowClick
}

// Usage in TableRow
<TableRow
  onClick={(e) => handleRowClick(index, e)}
  className="cursor-pointer"
  data-state={row.getIsSelected() && 'selected'}
>
```

### Select All Across Pages (Server-Side)

```typescript
// src/components/ui/data-table/bulk-selection-table.tsx
import { useState } from 'react'
import type { RowSelectionState } from '@tanstack/react-table'
import { Checkbox } from '@/components/ui/checkbox'
import { Button } from '@/components/ui/button'

type SelectionMode = 'none' | 'page' | 'all'

interface BulkSelectionState {
  mode: SelectionMode
  excludedIds: Set<string>
  includedIds: Set<string>
}

export function useBulkSelection(totalCount: number) {
  const [selection, setSelection] = useState<BulkSelectionState>({
    mode: 'none',
    excludedIds: new Set(),
    includedIds: new Set(),
  })

  const getSelectedCount = () => {
    if (selection.mode === 'none') {
      return selection.includedIds.size
    }
    if (selection.mode === 'all') {
      return totalCount - selection.excludedIds.size
    }
    return selection.includedIds.size
  }

  const isRowSelected = (id: string) => {
    if (selection.mode === 'all') {
      return !selection.excludedIds.has(id)
    }
    return selection.includedIds.has(id)
  }

  const toggleRow = (id: string) => {
    if (selection.mode === 'all') {
      const newExcluded = new Set(selection.excludedIds)
      if (newExcluded.has(id)) {
        newExcluded.delete(id)
      } else {
        newExcluded.add(id)
      }
      setSelection({ ...selection, excludedIds: newExcluded })
    } else {
      const newIncluded = new Set(selection.includedIds)
      if (newIncluded.has(id)) {
        newIncluded.delete(id)
      } else {
        newIncluded.add(id)
      }
      setSelection({ ...selection, includedIds: newIncluded })
    }
  }

  const selectAll = () => {
    setSelection({
      mode: 'all',
      excludedIds: new Set(),
      includedIds: new Set(),
    })
  }

  const clearSelection = () => {
    setSelection({
      mode: 'none',
      excludedIds: new Set(),
      includedIds: new Set(),
    })
  }

  return {
    selection,
    getSelectedCount,
    isRowSelected,
    toggleRow,
    selectAll,
    clearSelection,
  }
}

// Bulk selection header component
function BulkSelectionHeader({
  totalCount,
  pageRowCount,
  selectAll,
  clearSelection,
  getSelectedCount,
}: {
  totalCount: number
  pageRowCount: number
  selectAll: () => void
  clearSelection: () => void
  getSelectedCount: () => number
}) {
  const selectedCount = getSelectedCount()

  if (selectedCount === 0) return null

  return (
    <div className="flex items-center justify-between bg-muted px-4 py-2">
      <span className="text-sm">
        {selectedCount === totalCount
          ? `All ${totalCount} items selected`
          : `${selectedCount} item${selectedCount > 1 ? 's' : ''} selected`}
      </span>
      <div className="flex gap-2">
        {selectedCount < totalCount && (
          <Button variant="link" size="sm" onClick={selectAll}>
            Select all {totalCount} items
          </Button>
        )}
        <Button variant="link" size="sm" onClick={clearSelection}>
          Clear selection
        </Button>
      </div>
    </div>
  )
}
```

### Bulk Actions Pattern

```typescript
// src/components/features/bulk-actions-toolbar.tsx
import { useState } from 'react'
import { Button } from '@/components/ui/button'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu'
import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
  AlertDialogDescription,
  AlertDialogFooter,
  AlertDialogHeader,
  AlertDialogTitle,
} from '@/components/ui/alert-dialog'
import { Trash2, Archive, Tag, MoreHorizontal } from 'lucide-react'

interface BulkActionsToolbarProps {
  selectedCount: number
  onDelete: () => Promise<void>
  onArchive: () => Promise<void>
  onTag: (tag: string) => Promise<void>
}

export function BulkActionsToolbar({
  selectedCount,
  onDelete,
  onArchive,
  onTag,
}: BulkActionsToolbarProps) {
  const [showDeleteConfirm, setShowDeleteConfirm] = useState(false)
  const [isProcessing, setIsProcessing] = useState(false)

  if (selectedCount === 0) return null

  const handleDelete = async () => {
    setIsProcessing(true)
    try {
      await onDelete()
    } finally {
      setIsProcessing(false)
      setShowDeleteConfirm(false)
    }
  }

  return (
    <>
      <div className="flex items-center gap-2 bg-muted p-2 rounded-lg">
        <span className="text-sm font-medium px-2">
          {selectedCount} selected
        </span>
        <div className="h-4 w-px bg-border" />
        <Button
          variant="ghost"
          size="sm"
          onClick={onArchive}
          disabled={isProcessing}
        >
          <Archive className="h-4 w-4 mr-2" />
          Archive
        </Button>
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="ghost" size="sm" disabled={isProcessing}>
              <Tag className="h-4 w-4 mr-2" />
              Add Tag
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent>
            <DropdownMenuItem onClick={() => onTag('important')}>
              Important
            </DropdownMenuItem>
            <DropdownMenuItem onClick={() => onTag('review')}>
              Needs Review
            </DropdownMenuItem>
            <DropdownMenuItem onClick={() => onTag('approved')}>
              Approved
            </DropdownMenuItem>
          </DropdownMenuContent>
        </DropdownMenu>
        <Button
          variant="ghost"
          size="sm"
          className="text-destructive hover:text-destructive"
          onClick={() => setShowDeleteConfirm(true)}
          disabled={isProcessing}
        >
          <Trash2 className="h-4 w-4 mr-2" />
          Delete
        </Button>
      </div>

      <AlertDialog open={showDeleteConfirm} onOpenChange={setShowDeleteConfirm}>
        <AlertDialogContent>
          <AlertDialogHeader>
            <AlertDialogTitle>Delete {selectedCount} items?</AlertDialogTitle>
            <AlertDialogDescription>
              This action cannot be undone. These items will be permanently
              deleted.
            </AlertDialogDescription>
          </AlertDialogHeader>
          <AlertDialogFooter>
            <AlertDialogCancel>Cancel</AlertDialogCancel>
            <AlertDialogAction
              onClick={handleDelete}
              className="bg-destructive text-destructive-foreground hover:bg-destructive/90"
            >
              {isProcessing ? 'Deleting...' : 'Delete'}
            </AlertDialogAction>
          </AlertDialogFooter>
        </AlertDialogContent>
      </AlertDialog>
    </>
  )
}
```

---

## 7. Accessibility Patterns

WCAG 2.1 AA compliance for data tables.

### Screen Reader Announcements

```typescript
// src/hooks/use-live-region.ts
import { useCallback, useRef } from 'react'

export function useLiveRegion() {
  const regionRef = useRef<HTMLDivElement | null>(null)

  const announce = useCallback((message: string, priority: 'polite' | 'assertive' = 'polite') => {
    if (!regionRef.current) {
      // Create live region if it doesn't exist
      const region = document.createElement('div')
      region.setAttribute('role', 'status')
      region.setAttribute('aria-live', priority)
      region.setAttribute('aria-atomic', 'true')
      region.className = 'sr-only'
      document.body.appendChild(region)
      regionRef.current = region
    }

    // Clear and set message (triggers announcement)
    regionRef.current.textContent = ''
    requestAnimationFrame(() => {
      if (regionRef.current) {
        regionRef.current.textContent = message
      }
    })
  }, [])

  return announce
}

// Usage in sortable header
function SortableHeader({ column, label }: { column: any; label: string }) {
  const announce = useLiveRegion()

  const handleSort = () => {
    const currentSort = column.getIsSorted()
    column.toggleSorting(currentSort === 'asc')

    const newSort = currentSort === 'asc' ? 'descending' : 'ascending'
    announce(`Table sorted by ${label}, ${newSort}`)
  }

  return (
    <Button
      variant="ghost"
      onClick={handleSort}
      aria-label={`Sort by ${label}`}
      aria-sort={
        column.getIsSorted()
          ? column.getIsSorted() === 'asc'
            ? 'ascending'
            : 'descending'
          : 'none'
      }
    >
      {label}
      <ArrowUpDown className="ml-2 h-4 w-4" />
    </Button>
  )
}
```

### Keyboard Navigation Between Cells

```typescript
// src/hooks/use-grid-navigation.ts
import { useCallback, useRef } from 'react'

interface GridNavigation {
  onKeyDown: (e: React.KeyboardEvent, rowIndex: number, colIndex: number) => void
  registerCell: (rowIndex: number, colIndex: number, element: HTMLElement | null) => void
}

export function useGridNavigation(
  rowCount: number,
  colCount: number
): GridNavigation {
  const cellRefs = useRef<Map<string, HTMLElement>>(new Map())

  const registerCell = useCallback(
    (rowIndex: number, colIndex: number, element: HTMLElement | null) => {
      const key = `${rowIndex}-${colIndex}`
      if (element) {
        cellRefs.current.set(key, element)
      } else {
        cellRefs.current.delete(key)
      }
    },
    []
  )

  const focusCell = useCallback((rowIndex: number, colIndex: number) => {
    const key = `${rowIndex}-${colIndex}`
    const cell = cellRefs.current.get(key)
    if (cell) {
      const focusable = cell.querySelector<HTMLElement>(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      )
      ;(focusable ?? cell).focus()
    }
  }, [])

  const onKeyDown = useCallback(
    (e: React.KeyboardEvent, rowIndex: number, colIndex: number) => {
      let nextRow = rowIndex
      let nextCol = colIndex

      switch (e.key) {
        case 'ArrowUp':
          nextRow = Math.max(0, rowIndex - 1)
          break
        case 'ArrowDown':
          nextRow = Math.min(rowCount - 1, rowIndex + 1)
          break
        case 'ArrowLeft':
          nextCol = Math.max(0, colIndex - 1)
          break
        case 'ArrowRight':
          nextCol = Math.min(colCount - 1, colIndex + 1)
          break
        case 'Home':
          if (e.ctrlKey) {
            nextRow = 0
          }
          nextCol = 0
          break
        case 'End':
          if (e.ctrlKey) {
            nextRow = rowCount - 1
          }
          nextCol = colCount - 1
          break
        default:
          return
      }

      if (nextRow !== rowIndex || nextCol !== colIndex) {
        e.preventDefault()
        focusCell(nextRow, nextCol)
      }
    },
    [rowCount, colCount, focusCell]
  )

  return { onKeyDown, registerCell }
}

// Usage
function AccessibleTableCell({
  cell,
  rowIndex,
  colIndex,
  navigation,
}: {
  cell: any
  rowIndex: number
  colIndex: number
  navigation: GridNavigation
}) {
  return (
    <TableCell
      ref={(el) => navigation.registerCell(rowIndex, colIndex, el)}
      tabIndex={rowIndex === 0 && colIndex === 0 ? 0 : -1}
      onKeyDown={(e) => navigation.onKeyDown(e, rowIndex, colIndex)}
      role="gridcell"
    >
      {flexRender(cell.column.columnDef.cell, cell.getContext())}
    </TableCell>
  )
}
```

### Focus Management During Edit

```typescript
// src/components/ui/data-table/focus-trap-cell.tsx
import { useRef, useEffect } from 'react'

interface FocusTrapCellProps {
  isEditing: boolean
  onEscape: () => void
  children: React.ReactNode
}

export function FocusTrapCell({
  isEditing,
  onEscape,
  children,
}: FocusTrapCellProps) {
  const cellRef = useRef<HTMLTableCellElement>(null)
  const previousFocusRef = useRef<HTMLElement | null>(null)

  useEffect(() => {
    if (isEditing) {
      // Store current focus
      previousFocusRef.current = document.activeElement as HTMLElement

      // Focus first input in cell
      const input = cellRef.current?.querySelector<HTMLElement>('input, select, textarea')
      input?.focus()
    } else if (previousFocusRef.current) {
      // Restore focus when edit ends
      previousFocusRef.current.focus()
      previousFocusRef.current = null
    }
  }, [isEditing])

  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Escape' && isEditing) {
        onEscape()
      }
    }

    document.addEventListener('keydown', handleKeyDown)
    return () => document.removeEventListener('keydown', handleKeyDown)
  }, [isEditing, onEscape])

  return (
    <TableCell ref={cellRef} role="gridcell">
      {children}
    </TableCell>
  )
}
```

### Complete Accessible Table Structure

```typescript
// src/components/ui/data-table/accessible-table.tsx
<div role="region" aria-label="Users data table">
  {/* Table caption for screen readers */}
  <Table role="grid" aria-describedby="table-summary">
    <caption id="table-summary" className="sr-only">
      User data table with {data.length} rows. Sortable by Name and Email columns.
      Use arrow keys to navigate between cells.
    </caption>
    <TableHeader role="rowgroup">
      <TableRow role="row">
        {headers.map((header, index) => (
          <TableHead
            key={header.id}
            role="columnheader"
            scope="col"
            aria-sort={getSortDirection(header)}
          >
            {/* header content */}
          </TableHead>
        ))}
      </TableRow>
    </TableHeader>
    <TableBody role="rowgroup">
      {rows.map((row, rowIndex) => (
        <TableRow
          key={row.id}
          role="row"
          aria-selected={row.getIsSelected()}
          aria-rowindex={rowIndex + 2} // +2 for 1-based and header row
        >
          {row.getVisibleCells().map((cell, colIndex) => (
            <TableCell
              key={cell.id}
              role="gridcell"
              aria-colindex={colIndex + 1}
            >
              {/* cell content */}
            </TableCell>
          ))}
        </TableRow>
      ))}
    </TableBody>
  </Table>

  {/* Pagination status for screen readers */}
  <div role="status" aria-live="polite" className="sr-only">
    Showing page {currentPage} of {totalPages}. {rowCount} total results.
  </div>
</div>
```

---

## 8. Performance Optimization

Prevent unnecessary re-renders and optimize large datasets.

### Stable Column Definitions

```typescript
// BAD: Creates new column objects every render
function BadTable() {
  const columns: ColumnDef<User>[] = [
    { accessorKey: 'name', header: 'Name' },
    { accessorKey: 'email', header: 'Email' },
  ]

  return <DataTable columns={columns} data={data} />
}

// GOOD: Define outside component or memoize
const columns: ColumnDef<User>[] = [
  { accessorKey: 'name', header: 'Name' },
  { accessorKey: 'email', header: 'Email' },
]

function GoodTable() {
  return <DataTable columns={columns} data={data} />
}

// GOOD: Memoize when columns depend on props
function DynamicTable({ onEdit }: { onEdit: (user: User) => void }) {
  const columns = useMemo<ColumnDef<User>[]>(
    () => [
      { accessorKey: 'name', header: 'Name' },
      {
        id: 'actions',
        cell: ({ row }) => (
          <Button onClick={() => onEdit(row.original)}>Edit</Button>
        ),
      },
    ],
    [onEdit]
  )

  return <DataTable columns={columns} data={data} />
}
```

### Memoized Cell Renderers

```typescript
// src/components/ui/data-table/memoized-cells.tsx
import { memo } from 'react'

// Memoize complex cell components
const UserAvatarCell = memo(function UserAvatarCell({
  user,
}: {
  user: { name: string; avatar: string }
}) {
  return (
    <div className="flex items-center gap-2">
      <img
        src={user.avatar}
        alt=""
        className="h-8 w-8 rounded-full"
        loading="lazy"
      />
      <span>{user.name}</span>
    </div>
  )
})

// Usage in column definition
const columns: ColumnDef<User>[] = [
  {
    accessorKey: 'name',
    cell: ({ row }) => <UserAvatarCell user={row.original} />,
  },
]
```

### Avoiding Re-renders with Callbacks

```typescript
// BAD: Creates new function every render
function BadActionsCell({ row }: { row: Row<User> }) {
  return (
    <Button onClick={() => handleEdit(row.original)}>
      Edit
    </Button>
  )
}

// GOOD: Use useCallback or pass stable references
function GoodActionsCell({ row, onEdit }: { row: Row<User>; onEdit: (user: User) => void }) {
  const handleClick = useCallback(() => {
    onEdit(row.original)
  }, [row.original, onEdit])

  return <Button onClick={handleClick}>Edit</Button>
}

// BETTER: Create stable action handlers outside the component
function createActionsColumn<T>(
  actions: { label: string; handler: (item: T) => void }[]
): ColumnDef<T> {
  return {
    id: 'actions',
    cell: ({ row }) => (
      <ActionButtons actions={actions} item={row.original} />
    ),
  }
}

const ActionButtons = memo(function ActionButtons<T>({
  actions,
  item,
}: {
  actions: { label: string; handler: (item: T) => void }[]
  item: T
}) {
  return (
    <div className="flex gap-2">
      {actions.map((action) => (
        <Button
          key={action.label}
          variant="ghost"
          size="sm"
          onClick={() => action.handler(item)}
        >
          {action.label}
        </Button>
      ))}
    </div>
  )
})
```

### Data Stability

```typescript
// Ensure data reference stability when possible
function TableContainer() {
  // BAD: Creates new array every render
  const data = users.filter((u) => u.active)

  // GOOD: Memoize derived data
  const data = useMemo(
    () => users.filter((u) => u.active),
    [users]
  )

  // With React Query - data is stable between renders
  const { data = [] } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
  })

  return <DataTable columns={columns} data={data} />
}
```

### Performance Monitoring

```typescript
// src/hooks/use-table-performance.ts
import { useEffect, useRef } from 'react'

export function useTablePerformance(tableName: string, rowCount: number) {
  const renderCount = useRef(0)
  const lastRenderTime = useRef(performance.now())

  useEffect(() => {
    renderCount.current++
    const now = performance.now()
    const timeSinceLastRender = now - lastRenderTime.current
    lastRenderTime.current = now

    if (process.env.NODE_ENV === 'development') {
      if (timeSinceLastRender < 100 && renderCount.current > 1) {
        console.warn(
          `[${tableName}] Rapid re-render detected (${timeSinceLastRender.toFixed(1)}ms). ` +
          `Render #${renderCount.current} with ${rowCount} rows.`
        )
      }
    }
  })
}

// Usage
function DataTable({ data, columns }) {
  useTablePerformance('UsersTable', data.length)
  // ...
}
```

---

## Quick Reference

| Pattern | When to Use | Key Imports |
|---------|-------------|-------------|
| Server-side | 10K+ rows, complex filters | `manualPagination`, `manualSorting` |
| Row expansion | Detail views, hierarchical | `getExpandedRowModel` |
| Cell editing | Inline updates | Custom cell components |
| Column pinning | Wide tables with key columns | `ColumnPinningState` |
| Column resizing | User layout preferences | `columnResizeMode` |
| Column reorder | Customizable layouts | `@dnd-kit/core` + `@dnd-kit/sortable` |
| Virtualization | 1000+ rows | `@tanstack/react-virtual` |
| Range selection | Bulk operations | Custom shift+click handler |
| Grid navigation | Keyboard accessibility | Custom arrow key handler |

---

## Related Documentation

- [SKILL.md](./SKILL.md) - Basic table setup
- [ds-virtualize](../ds-virtualize/SKILL.md) - List virtualization
- [ds-accessibility](../ds-accessibility/SKILL.md) - A11y patterns
- [LAYER-07-INTERACTION-PATTERNS](../../system/LAYER-07-INTERACTION-PATTERNS.md) - Loading states
