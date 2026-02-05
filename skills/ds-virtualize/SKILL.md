---
skill: ds-virtualize
description: Generate virtualized list implementations using TanStack Virtual for performance with large datasets
triggers:
  - "virtualize list"
  - "virtual scroll"
  - "ds virtualize"
  - "large list performance"
  - "infinite scroll"
---

# List Virtualization

Generate virtualized list implementations using TanStack Virtual for optimal performance with large datasets.

## When to Use

- Lists with 100+ items
- Complex item rendering (images, forms, interactions)
- Infinite scroll requirements
- Memory-constrained environments

## Prerequisites

```bash
npm install @tanstack/react-virtual
```

## Basic Virtual List

```typescript
// src/components/features/virtual-list.tsx
import { useRef } from 'react'
import { useVirtualizer } from '@tanstack/react-virtual'

interface VirtualListProps<T> {
  items: T[]
  renderItem: (item: T, index: number) => React.ReactNode
  estimateSize?: number
  overscan?: number
  className?: string
}

export function VirtualList<T>({
  items,
  renderItem,
  estimateSize = 50,
  overscan = 5,
  className = 'h-[400px]',
}: VirtualListProps<T>) {
  const parentRef = useRef<HTMLDivElement>(null)

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => estimateSize,
    overscan,
  })

  return (
    <div ref={parentRef} className={`overflow-auto ${className}`}>
      <div
        style={{ height: `${virtualizer.getTotalSize()}px` }}
        className="relative w-full"
      >
        {virtualizer.getVirtualItems().map((virtualItem) => (
          <div
            key={virtualItem.key}
            className="absolute left-0 top-0 w-full"
            style={{ transform: `translateY(${virtualItem.start}px)` }}
          >
            {renderItem(items[virtualItem.index], virtualItem.index)}
          </div>
        ))}
      </div>
    </div>
  )
}
```

## Usage Example

```typescript
// Using the VirtualList component
import { VirtualList } from '@/components/features/virtual-list'

interface User {
  id: string
  name: string
  email: string
}

function UserListPage({ users }: { users: User[] }) {
  return (
    <VirtualList
      items={users}
      estimateSize={72}
      className="h-[600px] border rounded-lg"
      renderItem={(user) => (
        <div className="flex items-center gap-4 p-4 border-b">
          <div className="h-10 w-10 rounded-full bg-primary/10" />
          <div>
            <p className="font-medium">{user.name}</p>
            <p className="text-sm text-muted-foreground">{user.email}</p>
          </div>
        </div>
      )}
    />
  )
}
```

## Variable Height Items

For items with variable heights, measure after render:

```typescript
import { useRef } from 'react'
import { useVirtualizer } from '@tanstack/react-virtual'

export function VariableHeightList({ items }: { items: string[] }) {
  const parentRef = useRef<HTMLDivElement>(null)

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 100, // Initial estimate
    measureElement: (element) => element.getBoundingClientRect().height,
  })

  return (
    <div ref={parentRef} className="h-[400px] overflow-auto">
      <div
        style={{ height: `${virtualizer.getTotalSize()}px` }}
        className="relative w-full"
      >
        {virtualizer.getVirtualItems().map((virtualItem) => (
          <div
            key={virtualItem.key}
            data-index={virtualItem.index}
            ref={virtualizer.measureElement}
            className="absolute left-0 top-0 w-full"
            style={{ transform: `translateY(${virtualItem.start}px)` }}
          >
            <div className="p-4 border-b">
              {items[virtualItem.index]}
            </div>
          </div>
        ))}
      </div>
    </div>
  )
}
```

## Infinite Scroll Pattern

```typescript
import { useRef, useEffect } from 'react'
import { useInfiniteQuery } from '@tanstack/react-query'
import { useVirtualizer } from '@tanstack/react-virtual'

export function InfiniteVirtualList() {
  const parentRef = useRef<HTMLDivElement>(null)

  const { data, fetchNextPage, hasNextPage, isFetchingNextPage } = useInfiniteQuery({
    queryKey: ['items'],
    queryFn: ({ pageParam = 0 }) => fetchItems(pageParam),
    getNextPageParam: (lastPage) => lastPage.nextCursor,
    initialPageParam: 0,
  })

  const allItems = data?.pages.flatMap((page) => page.items) ?? []

  const virtualizer = useVirtualizer({
    count: hasNextPage ? allItems.length + 1 : allItems.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 50,
    overscan: 5,
  })

  const virtualItems = virtualizer.getVirtualItems()

  // Fetch more when nearing end
  useEffect(() => {
    const lastItem = virtualItems[virtualItems.length - 1]
    if (!lastItem) return

    if (lastItem.index >= allItems.length - 1 && hasNextPage && !isFetchingNextPage) {
      fetchNextPage()
    }
  }, [virtualItems, hasNextPage, isFetchingNextPage, allItems.length, fetchNextPage])

  return (
    <div ref={parentRef} className="h-[600px] overflow-auto">
      <div
        style={{ height: `${virtualizer.getTotalSize()}px` }}
        className="relative w-full"
      >
        {virtualItems.map((virtualItem) => {
          const isLoader = virtualItem.index >= allItems.length

          return (
            <div
              key={virtualItem.key}
              className="absolute left-0 top-0 w-full"
              style={{ transform: `translateY(${virtualItem.start}px)` }}
            >
              {isLoader ? (
                <div className="p-4 text-center text-muted-foreground">
                  Loading more...
                </div>
              ) : (
                <ItemRow item={allItems[virtualItem.index]} />
              )}
            </div>
          )
        })}
      </div>
    </div>
  )
}
```

## Performance Tips

| Tip | Why |
|-----|-----|
| Use `overscan={5}` | Renders extra items above/below viewport for smooth scrolling |
| Memoize `renderItem` | Prevents unnecessary re-renders of visible items |
| Set accurate `estimateSize` | Reduces layout shifts and scroll position jumps |
| Use CSS `transform` | GPU-accelerated positioning vs `top` property |

## Accessibility Considerations

```typescript
<div
  ref={parentRef}
  role="list"
  aria-label="User list"
  className="h-[400px] overflow-auto"
>
  {/* Virtual items with role="listitem" */}
</div>
```

## Next Steps

- Use `/ds-data-table` for virtualized tables with sorting/filtering
- See [LAYER-07-INTERACTION-PATTERNS](../../system/LAYER-07-INTERACTION-PATTERNS.md) for loading states
