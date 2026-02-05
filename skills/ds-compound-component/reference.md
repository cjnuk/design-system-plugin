# Compound Component Advanced Patterns Reference

This reference covers advanced patterns for building sophisticated compound components. For basic compound component patterns (Context + Parent.Child syntax), see [SKILL.md](./SKILL.md).

---

## Table of Contents

1. [Render Props Pattern](#1-render-props-pattern)
2. [Polymorphic Components](#2-polymorphic-components)
3. [Slot Pattern](#3-slot-pattern)
4. [Headless Components](#4-headless-components)
5. [Complex State Sharing](#5-complex-state-sharing)
6. [Composition Validation](#6-composition-validation)
7. [Forwarding Refs](#7-forwarding-refs)
8. [Testing Strategies](#8-testing-strategies)

---

## 1. Render Props Pattern

When context alone is not enough, render props expose internal state directly to consumers.

### When Context Is Not Enough

Context works well for sharing state downward, but sometimes consumers need:
- Access to internal state for conditional rendering
- Fine-grained control over what gets rendered
- Ability to manipulate state inline

### Basic Render Props Implementation

```typescript
// src/components/ui/toggle-group/toggle-group.tsx
import { useState, type ReactNode } from 'react'

interface ToggleGroupState {
  selectedValue: string | null
  isExpanded: boolean
}

interface ToggleGroupActions {
  select: (value: string) => void
  toggle: () => void
  reset: () => void
}

type RenderProps = ToggleGroupState & ToggleGroupActions

interface ToggleGroupProps {
  defaultValue?: string
  children: ReactNode | ((props: RenderProps) => ReactNode)
}

export function ToggleGroup({ defaultValue, children }: ToggleGroupProps) {
  const [selectedValue, setSelectedValue] = useState<string | null>(defaultValue ?? null)
  const [isExpanded, setIsExpanded] = useState(false)

  const renderProps: RenderProps = {
    selectedValue,
    isExpanded,
    select: setSelectedValue,
    toggle: () => setIsExpanded(prev => !prev),
    reset: () => {
      setSelectedValue(defaultValue ?? null)
      setIsExpanded(false)
    },
  }

  return (
    <div className="flex flex-col gap-2">
      {typeof children === 'function' ? children(renderProps) : children}
    </div>
  )
}
```

### Consumer Usage

```tsx
import { ToggleGroup } from '@/components/ui/toggle-group'
import { Button } from '@/components/ui/button'

export function FilterPanel() {
  return (
    <ToggleGroup defaultValue="all">
      {({ selectedValue, isExpanded, select, toggle, reset }) => (
        <>
          <div className="flex gap-2">
            <Button
              variant={selectedValue === 'all' ? 'primary' : 'outline'}
              onClick={() => select('all')}
            >
              All
            </Button>
            <Button
              variant={selectedValue === 'active' ? 'primary' : 'outline'}
              onClick={() => select('active')}
            >
              Active
            </Button>
            <Button
              variant={selectedValue === 'archived' ? 'primary' : 'outline'}
              onClick={() => select('archived')}
            >
              Archived
            </Button>
          </div>

          {isExpanded && (
            <div className="p-4 bg-surface-2 rounded-lg">
              <p className="text-sm text-muted-foreground">
                Currently showing: {selectedValue}
              </p>
            </div>
          )}

          <div className="flex gap-2">
            <Button variant="ghost" size="sm" onClick={toggle}>
              {isExpanded ? 'Hide' : 'Show'} details
            </Button>
            <Button variant="ghost" size="sm" onClick={reset}>
              Reset
            </Button>
          </div>
        </>
      )}
    </ToggleGroup>
  )
}
```

### Type-Safe Render Props with Generics

```typescript
// Generic render props for type-safe item selection
interface SelectableGroupProps<T> {
  items: T[]
  getKey: (item: T) => string
  children: (props: {
    items: T[]
    selectedItem: T | null
    selectItem: (item: T) => void
    isSelected: (item: T) => boolean
  }) => ReactNode
}

export function SelectableGroup<T>({
  items,
  getKey,
  children,
}: SelectableGroupProps<T>) {
  const [selectedKey, setSelectedKey] = useState<string | null>(null)

  const selectedItem = items.find(item => getKey(item) === selectedKey) ?? null

  return children({
    items,
    selectedItem,
    selectItem: (item) => setSelectedKey(getKey(item)),
    isSelected: (item) => getKey(item) === selectedKey,
  })
}

// Usage with full type inference
interface User {
  id: string
  name: string
  email: string
}

<SelectableGroup items={users} getKey={(u) => u.id}>
  {({ items, selectedItem, selectItem, isSelected }) => (
    <ul>
      {items.map(user => (
        <li
          key={user.id}
          onClick={() => selectItem(user)}
          className={isSelected(user) ? 'bg-primary-100' : ''}
        >
          {user.name} {/* TypeScript knows this is User */}
        </li>
      ))}
    </ul>
  )}
</SelectableGroup>
```

---

## 2. Polymorphic Components

The "as" prop pattern allows components to render as different HTML elements or other components while maintaining type safety.

### Basic Polymorphic Component

```typescript
// src/components/ui/box/box.tsx
import {
  type ElementType,
  type ComponentPropsWithoutRef,
  type ReactNode,
  forwardRef,
} from 'react'

type BoxProps<E extends ElementType = 'div'> = {
  as?: E
  children?: ReactNode
  className?: string
} & Omit<ComponentPropsWithoutRef<E>, 'as' | 'children' | 'className'>

type BoxComponent = <E extends ElementType = 'div'>(
  props: BoxProps<E> & { ref?: React.Ref<HTMLElement> }
) => ReactNode

export const Box: BoxComponent = forwardRef(function Box<
  E extends ElementType = 'div'
>(
  { as, children, className, ...props }: BoxProps<E>,
  ref: React.Ref<HTMLElement>
) {
  const Component = as ?? 'div'

  return (
    <Component ref={ref} className={className} {...props}>
      {children}
    </Component>
  )
})
```

### Type-Safe Polymorphism with Proper Prop Merging

```typescript
// src/lib/polymorphic.ts
import type {
  ElementType,
  ComponentPropsWithoutRef,
  ComponentPropsWithRef,
} from 'react'

// Extract the ref type for an element
type PolymorphicRef<E extends ElementType> =
  ComponentPropsWithRef<E>['ref']

// Props that the component itself defines
type AsProp<E extends ElementType> = { as?: E }

// Props to omit from the element's native props
type PropsToOmit<E extends ElementType, P> = keyof (AsProp<E> & P)

// Final polymorphic props type
export type PolymorphicProps<
  E extends ElementType,
  Props = object
> = Props &
  AsProp<E> &
  Omit<ComponentPropsWithoutRef<E>, PropsToOmit<E, Props>>

// With ref support
export type PolymorphicPropsWithRef<
  E extends ElementType,
  Props = object
> = PolymorphicProps<E, Props> & { ref?: PolymorphicRef<E> }
```

### Polymorphic Button Example

```typescript
// src/components/ui/button/button.tsx
import { forwardRef, type ElementType } from 'react'
import { cn } from '@/lib/utils'
import type { PolymorphicPropsWithRef } from '@/lib/polymorphic'

const buttonVariants = {
  primary: 'bg-primary-500 text-white hover:bg-primary-600',
  secondary: 'bg-slate-200 text-slate-900 hover:bg-slate-300',
  outline: 'border border-primary-500 text-primary-500 hover:bg-primary-50',
  ghost: 'text-slate-600 hover:bg-slate-100',
  destructive: 'bg-red-500 text-white hover:bg-red-600',
} as const

const buttonSizes = {
  sm: 'px-3 py-1.5 text-sm',
  md: 'px-4 py-2 text-base',
  lg: 'px-6 py-3 text-lg',
} as const

interface ButtonOwnProps {
  variant?: keyof typeof buttonVariants
  size?: keyof typeof buttonSizes
  isLoading?: boolean
}

type ButtonProps<E extends ElementType = 'button'> = PolymorphicPropsWithRef<
  E,
  ButtonOwnProps
>

type ButtonComponent = <E extends ElementType = 'button'>(
  props: ButtonProps<E>
) => React.ReactNode

export const Button: ButtonComponent = forwardRef(function Button<
  E extends ElementType = 'button'
>(
  {
    as,
    variant = 'primary',
    size = 'md',
    isLoading = false,
    className,
    children,
    disabled,
    ...props
  }: ButtonProps<E>,
  ref: React.Ref<Element>
) {
  const Component = as ?? 'button'
  const isDisabled = disabled || isLoading

  return (
    <Component
      ref={ref}
      disabled={isDisabled}
      className={cn(
        'inline-flex items-center justify-center rounded font-medium',
        'transition-colors duration-150 ease-out',
        'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-500',
        'disabled:opacity-50 disabled:pointer-events-none',
        buttonVariants[variant],
        buttonSizes[size],
        className
      )}
      {...props}
    >
      {isLoading && (
        <svg
          className="animate-spin -ml-1 mr-2 h-4 w-4"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            className="opacity-25"
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            strokeWidth="4"
          />
          <path
            className="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"
          />
        </svg>
      )}
      {children}
    </Component>
  )
})
```

### Usage Examples

```tsx
import { Button } from '@/components/ui/button'
import Link from 'next/link'

// Default: renders as <button>
<Button variant="primary" onClick={handleClick}>
  Click me
</Button>

// Render as <a> with proper types
<Button as="a" href="/about" variant="outline">
  Learn more
</Button>

// Render as Next.js Link
<Button as={Link} href="/dashboard" variant="secondary">
  Go to Dashboard
</Button>

// TypeScript catches errors
<Button as="a" onClick={handleClick}> {/* onClick is valid on <a> */}
  Link button
</Button>

<Button as="input" type="submit" /> {/* input props are valid */}
```

---

## 3. Slot Pattern

Slots provide named areas for content injection, offering more control than a single children prop.

### Named Slots vs Children

```typescript
// src/components/ui/card/card-context.tsx
import { createContext, useContext, type ReactNode } from 'react'

interface CardSlots {
  header?: ReactNode
  media?: ReactNode
  content?: ReactNode
  actions?: ReactNode
}

interface CardContextValue {
  slots: CardSlots
  variant: 'elevated' | 'outlined' | 'filled'
}

const CardContext = createContext<CardContextValue | null>(null)

export function useCardContext() {
  const context = useContext(CardContext)
  if (!context) {
    throw new Error('Card components must be used within a Card')
  }
  return context
}

export { CardContext }
export type { CardSlots, CardContextValue }
```

### Slot Implementation with Default Content

```typescript
// src/components/ui/card/card.tsx
import { type ReactNode, useMemo } from 'react'
import { cn } from '@/lib/utils'
import { CardContext, type CardSlots } from './card-context'

interface CardProps {
  children?: ReactNode
  slots?: CardSlots
  variant?: 'elevated' | 'outlined' | 'filled'
  className?: string
}

const variantStyles = {
  elevated: 'bg-white shadow-md',
  outlined: 'bg-white border border-slate-200',
  filled: 'bg-slate-100',
} as const

export function Card({
  children,
  slots = {},
  variant = 'elevated',
  className,
}: CardProps) {
  const contextValue = useMemo(
    () => ({ slots, variant }),
    [slots, variant]
  )

  // Default slot content
  const defaultHeader = null
  const defaultMedia = null
  const defaultContent = children
  const defaultActions = null

  return (
    <CardContext.Provider value={contextValue}>
      <article
        className={cn(
          'rounded-lg overflow-hidden',
          variantStyles[variant],
          className
        )}
      >
        {/* Header Slot */}
        {(slots.header ?? defaultHeader) && (
          <div className="px-4 py-3 border-b border-slate-200">
            {slots.header ?? defaultHeader}
          </div>
        )}

        {/* Media Slot */}
        {(slots.media ?? defaultMedia) && (
          <div className="relative aspect-video">
            {slots.media ?? defaultMedia}
          </div>
        )}

        {/* Content Slot */}
        <div className="p-4">
          {slots.content ?? defaultContent}
        </div>

        {/* Actions Slot */}
        {(slots.actions ?? defaultActions) && (
          <div className="px-4 py-3 border-t border-slate-200 flex justify-end gap-2">
            {slots.actions ?? defaultActions}
          </div>
        )}
      </article>
    </CardContext.Provider>
  )
}
```

### Slot Validation

```typescript
// src/components/ui/card/card-with-validation.tsx
import { Children, isValidElement, type ReactNode, type ReactElement } from 'react'

interface SlotConfig {
  required?: boolean
  maxCount?: number
  allowedTypes?: readonly string[]
}

interface SlotsConfig {
  [slotName: string]: SlotConfig
}

function validateSlots(
  slots: Record<string, ReactNode>,
  config: SlotsConfig
): void {
  for (const [slotName, slotConfig] of Object.entries(config)) {
    const slotContent = slots[slotName]

    // Check required slots
    if (slotConfig.required && !slotContent) {
      console.error(
        `[Card] Required slot "${slotName}" is missing. ` +
        `Provide content via the slots.${slotName} prop.`
      )
    }

    // Check max count
    if (slotContent && slotConfig.maxCount) {
      const count = Children.count(slotContent)
      if (count > slotConfig.maxCount) {
        console.error(
          `[Card] Slot "${slotName}" has ${count} children, ` +
          `but maximum is ${slotConfig.maxCount}.`
        )
      }
    }

    // Check allowed types
    if (slotContent && slotConfig.allowedTypes) {
      Children.forEach(slotContent, (child) => {
        if (isValidElement(child)) {
          const typeName =
            typeof child.type === 'string'
              ? child.type
              : child.type.displayName ?? child.type.name ?? 'Unknown'

          if (!slotConfig.allowedTypes!.includes(typeName)) {
            console.error(
              `[Card] Slot "${slotName}" received "${typeName}", ` +
              `but only allows: ${slotConfig.allowedTypes!.join(', ')}.`
            )
          }
        }
      })
    }
  }
}

const CARD_SLOTS_CONFIG: SlotsConfig = {
  header: { maxCount: 1 },
  media: { maxCount: 1, allowedTypes: ['img', 'video', 'CardMedia'] },
  content: { required: true },
  actions: { allowedTypes: ['Button', 'IconButton'] },
} as const

export function CardWithValidation({
  slots = {},
  children,
  ...props
}: CardProps) {
  if (process.env.NODE_ENV === 'development') {
    validateSlots({ ...slots, content: slots.content ?? children }, CARD_SLOTS_CONFIG)
  }

  return <Card slots={slots} {...props}>{children}</Card>
}
```

### Consumer Usage

```tsx
import { Card } from '@/components/ui/card'
import { Button } from '@/components/ui/button'
import Image from 'next/image'

export function ProductCard({ product }: { product: Product }) {
  return (
    <Card
      variant="outlined"
      slots={{
        header: (
          <div className="flex justify-between items-center">
            <h3 className="font-semibold">{product.name}</h3>
            <span className="text-primary-500 font-bold">
              ${product.price}
            </span>
          </div>
        ),
        media: (
          <Image
            src={product.image}
            alt={product.name}
            fill
            className="object-cover"
          />
        ),
        content: (
          <p className="text-slate-600 text-sm">
            {product.description}
          </p>
        ),
        actions: (
          <>
            <Button variant="outline" size="sm">
              Add to Wishlist
            </Button>
            <Button variant="primary" size="sm">
              Add to Cart
            </Button>
          </>
        ),
      }}
    />
  )
}
```

---

## 4. Headless Components

Headless components separate logic from presentation, providing maximum flexibility.

### Hook-Based API Alongside Compound Pattern

```typescript
// src/components/ui/accordion/use-accordion.ts
import { useState, useCallback, useMemo } from 'react'

interface UseAccordionOptions {
  type?: 'single' | 'multiple'
  defaultValue?: string | string[]
  collapsible?: boolean
}

interface UseAccordionReturn {
  // State
  expandedItems: Set<string>

  // Actions
  toggle: (value: string) => void
  expand: (value: string) => void
  collapse: (value: string) => void
  expandAll: (values: string[]) => void
  collapseAll: () => void

  // Helpers
  isExpanded: (value: string) => boolean
  getItemProps: (value: string) => {
    'data-state': 'open' | 'closed'
    'aria-expanded': boolean
  }
  getTriggerProps: (value: string) => {
    onClick: () => void
    'aria-expanded': boolean
    'aria-controls': string
  }
  getContentProps: (value: string) => {
    id: string
    'data-state': 'open' | 'closed'
    hidden: boolean
  }
}

export function useAccordion({
  type = 'single',
  defaultValue,
  collapsible = true,
}: UseAccordionOptions = {}): UseAccordionReturn {
  const [expandedItems, setExpandedItems] = useState<Set<string>>(() => {
    if (!defaultValue) return new Set()
    return new Set(Array.isArray(defaultValue) ? defaultValue : [defaultValue])
  })

  const toggle = useCallback(
    (value: string) => {
      setExpandedItems((prev) => {
        const next = new Set(prev)

        if (next.has(value)) {
          // Collapsing
          if (collapsible || next.size > 1) {
            next.delete(value)
          }
        } else {
          // Expanding
          if (type === 'single') {
            next.clear()
          }
          next.add(value)
        }

        return next
      })
    },
    [type, collapsible]
  )

  const expand = useCallback(
    (value: string) => {
      setExpandedItems((prev) => {
        if (type === 'single') {
          return new Set([value])
        }
        return new Set(prev).add(value)
      })
    },
    [type]
  )

  const collapse = useCallback((value: string) => {
    setExpandedItems((prev) => {
      const next = new Set(prev)
      next.delete(value)
      return next
    })
  }, [])

  const expandAll = useCallback((values: string[]) => {
    if (type === 'multiple') {
      setExpandedItems(new Set(values))
    }
  }, [type])

  const collapseAll = useCallback(() => {
    setExpandedItems(new Set())
  }, [])

  const isExpanded = useCallback(
    (value: string) => expandedItems.has(value),
    [expandedItems]
  )

  const getItemProps = useCallback(
    (value: string) => ({
      'data-state': expandedItems.has(value) ? 'open' : 'closed' as const,
      'aria-expanded': expandedItems.has(value),
    }),
    [expandedItems]
  )

  const getTriggerProps = useCallback(
    (value: string) => ({
      onClick: () => toggle(value),
      'aria-expanded': expandedItems.has(value),
      'aria-controls': `accordion-content-${value}`,
    }),
    [expandedItems, toggle]
  )

  const getContentProps = useCallback(
    (value: string) => ({
      id: `accordion-content-${value}`,
      'data-state': expandedItems.has(value) ? 'open' : 'closed' as const,
      hidden: !expandedItems.has(value),
    }),
    [expandedItems]
  )

  return useMemo(
    () => ({
      expandedItems,
      toggle,
      expand,
      collapse,
      expandAll,
      collapseAll,
      isExpanded,
      getItemProps,
      getTriggerProps,
      getContentProps,
    }),
    [
      expandedItems,
      toggle,
      expand,
      collapse,
      expandAll,
      collapseAll,
      isExpanded,
      getItemProps,
      getTriggerProps,
      getContentProps,
    ]
  )
}
```

### Styled Compound Component Using the Hook

```typescript
// src/components/ui/accordion/accordion.tsx
import { createContext, useContext, type ReactNode } from 'react'
import { cn } from '@/lib/utils'
import { useAccordion, type UseAccordionReturn } from './use-accordion'

const AccordionContext = createContext<UseAccordionReturn | null>(null)

function useAccordionContext() {
  const context = useContext(AccordionContext)
  if (!context) {
    throw new Error('Accordion components must be used within an Accordion')
  }
  return context
}

// Root
interface AccordionProps {
  children: ReactNode
  type?: 'single' | 'multiple'
  defaultValue?: string | string[]
  collapsible?: boolean
  className?: string
}

function AccordionRoot({
  children,
  type,
  defaultValue,
  collapsible,
  className,
}: AccordionProps) {
  const accordion = useAccordion({ type, defaultValue, collapsible })

  return (
    <AccordionContext.Provider value={accordion}>
      <div className={cn('divide-y divide-slate-200', className)}>
        {children}
      </div>
    </AccordionContext.Provider>
  )
}

// Item
interface AccordionItemProps {
  value: string
  children: ReactNode
  className?: string
}

function AccordionItem({ value, children, className }: AccordionItemProps) {
  const { getItemProps } = useAccordionContext()

  return (
    <div {...getItemProps(value)} className={cn('py-2', className)}>
      {children}
    </div>
  )
}

// Trigger
interface AccordionTriggerProps {
  value: string
  children: ReactNode
  className?: string
}

function AccordionTrigger({ value, children, className }: AccordionTriggerProps) {
  const { getTriggerProps, isExpanded } = useAccordionContext()

  return (
    <button
      {...getTriggerProps(value)}
      className={cn(
        'flex w-full items-center justify-between py-3 font-medium',
        'transition-colors hover:bg-slate-50 rounded',
        className
      )}
    >
      {children}
      <svg
        className={cn(
          'h-4 w-4 transition-transform duration-200',
          isExpanded(value) && 'rotate-180'
        )}
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          strokeLinecap="round"
          strokeLinejoin="round"
          strokeWidth={2}
          d="M19 9l-7 7-7-7"
        />
      </svg>
    </button>
  )
}

// Content
interface AccordionContentProps {
  value: string
  children: ReactNode
  className?: string
}

function AccordionContent({ value, children, className }: AccordionContentProps) {
  const { getContentProps, isExpanded } = useAccordionContext()
  const props = getContentProps(value)

  return (
    <div
      {...props}
      className={cn(
        'overflow-hidden transition-all duration-200',
        isExpanded(value)
          ? 'max-h-96 opacity-100'
          : 'max-h-0 opacity-0',
        className
      )}
    >
      <div className="pb-4 pt-1 text-slate-600">{children}</div>
    </div>
  )
}

// Assemble
export const Accordion = Object.assign(AccordionRoot, {
  Item: AccordionItem,
  Trigger: AccordionTrigger,
  Content: AccordionContent,
})

// Also export hook for headless usage
export { useAccordion }
```

### When to Go Headless

| Use Case | Recommendation |
|----------|----------------|
| Standard UI with theme | Use styled compound component |
| Custom design system | Use hook, build your own UI |
| Complex animations | Use hook for state, custom render |
| Integration with other libraries | Use hook for logic sharing |
| Radix/shadcn available | Use those; they are battle-tested |

---

## 5. Complex State Sharing

### Multiple Contexts (State vs Actions)

Separating state and actions prevents unnecessary re-renders when only actions are needed.

```typescript
// src/components/ui/data-table/data-table-context.tsx
import {
  createContext,
  useContext,
  useReducer,
  useMemo,
  type ReactNode,
  type Dispatch,
} from 'react'

// State shape
interface DataTableState<T> {
  data: T[]
  selectedRows: Set<string>
  sortColumn: string | null
  sortDirection: 'asc' | 'desc'
  currentPage: number
  pageSize: number
  filters: Record<string, unknown>
}

// Actions
type DataTableAction<T> =
  | { type: 'SELECT_ROW'; id: string }
  | { type: 'DESELECT_ROW'; id: string }
  | { type: 'SELECT_ALL'; ids: string[] }
  | { type: 'DESELECT_ALL' }
  | { type: 'SET_SORT'; column: string }
  | { type: 'SET_PAGE'; page: number }
  | { type: 'SET_PAGE_SIZE'; size: number }
  | { type: 'SET_FILTER'; key: string; value: unknown }
  | { type: 'SET_DATA'; data: T[] }

// Reducer
function dataTableReducer<T>(
  state: DataTableState<T>,
  action: DataTableAction<T>
): DataTableState<T> {
  switch (action.type) {
    case 'SELECT_ROW': {
      const next = new Set(state.selectedRows)
      next.add(action.id)
      return { ...state, selectedRows: next }
    }
    case 'DESELECT_ROW': {
      const next = new Set(state.selectedRows)
      next.delete(action.id)
      return { ...state, selectedRows: next }
    }
    case 'SELECT_ALL':
      return { ...state, selectedRows: new Set(action.ids) }
    case 'DESELECT_ALL':
      return { ...state, selectedRows: new Set() }
    case 'SET_SORT':
      return {
        ...state,
        sortColumn: action.column,
        sortDirection:
          state.sortColumn === action.column && state.sortDirection === 'asc'
            ? 'desc'
            : 'asc',
      }
    case 'SET_PAGE':
      return { ...state, currentPage: action.page }
    case 'SET_PAGE_SIZE':
      return { ...state, pageSize: action.size, currentPage: 1 }
    case 'SET_FILTER':
      return {
        ...state,
        filters: { ...state.filters, [action.key]: action.value },
        currentPage: 1,
      }
    case 'SET_DATA':
      return { ...state, data: action.data }
    default:
      return state
  }
}

// Separate contexts
const DataTableStateContext = createContext<DataTableState<unknown> | null>(null)
const DataTableDispatchContext = createContext<Dispatch<DataTableAction<unknown>> | null>(null)

// Hooks
export function useDataTableState<T>(): DataTableState<T> {
  const context = useContext(DataTableStateContext)
  if (!context) {
    throw new Error('useDataTableState must be used within a DataTableProvider')
  }
  return context as DataTableState<T>
}

export function useDataTableDispatch<T>(): Dispatch<DataTableAction<T>> {
  const context = useContext(DataTableDispatchContext)
  if (!context) {
    throw new Error('useDataTableDispatch must be used within a DataTableProvider')
  }
  return context as Dispatch<DataTableAction<T>>
}

// Combined hook for convenience
export function useDataTable<T>() {
  return {
    state: useDataTableState<T>(),
    dispatch: useDataTableDispatch<T>(),
  }
}

// Provider
interface DataTableProviderProps<T> {
  children: ReactNode
  initialData: T[]
  initialPageSize?: number
}

export function DataTableProvider<T>({
  children,
  initialData,
  initialPageSize = 10,
}: DataTableProviderProps<T>) {
  const [state, dispatch] = useReducer(dataTableReducer<T>, {
    data: initialData,
    selectedRows: new Set(),
    sortColumn: null,
    sortDirection: 'asc',
    currentPage: 1,
    pageSize: initialPageSize,
    filters: {},
  })

  return (
    <DataTableStateContext.Provider value={state as DataTableState<unknown>}>
      <DataTableDispatchContext.Provider value={dispatch as Dispatch<DataTableAction<unknown>>}>
        {children}
      </DataTableDispatchContext.Provider>
    </DataTableStateContext.Provider>
  )
}
```

### Performance: Avoiding Context Re-renders

```typescript
// src/components/ui/data-table/data-table-selectors.tsx
import { useMemo } from 'react'
import { useDataTableState } from './data-table-context'

// Memoized selectors prevent re-renders when unrelated state changes
export function useSelectedCount(): number {
  const { selectedRows } = useDataTableState()
  return selectedRows.size
}

export function usePaginatedData<T>(): T[] {
  const { data, currentPage, pageSize, sortColumn, sortDirection } =
    useDataTableState<T>()

  return useMemo(() => {
    let result = [...data]

    // Sort
    if (sortColumn) {
      result.sort((a, b) => {
        const aVal = (a as Record<string, unknown>)[sortColumn]
        const bVal = (b as Record<string, unknown>)[sortColumn]
        const comparison = String(aVal).localeCompare(String(bVal))
        return sortDirection === 'asc' ? comparison : -comparison
      })
    }

    // Paginate
    const start = (currentPage - 1) * pageSize
    return result.slice(start, start + pageSize)
  }, [data, currentPage, pageSize, sortColumn, sortDirection])
}

export function useIsRowSelected(id: string): boolean {
  const { selectedRows } = useDataTableState()
  // This only causes re-render when selectedRows changes
  return selectedRows.has(id)
}
```

### State Machines in Compound Components

```typescript
// src/components/ui/wizard/wizard-machine.ts
interface WizardState {
  status: 'idle' | 'active' | 'validating' | 'submitting' | 'complete' | 'error'
  currentStep: number
  totalSteps: number
  stepData: Record<number, unknown>
  error: string | null
}

type WizardEvent =
  | { type: 'START' }
  | { type: 'NEXT'; data: unknown }
  | { type: 'PREV' }
  | { type: 'GOTO'; step: number }
  | { type: 'VALIDATE_SUCCESS' }
  | { type: 'VALIDATE_ERROR'; error: string }
  | { type: 'SUBMIT' }
  | { type: 'SUBMIT_SUCCESS' }
  | { type: 'SUBMIT_ERROR'; error: string }
  | { type: 'RESET' }

function wizardReducer(state: WizardState, event: WizardEvent): WizardState {
  switch (state.status) {
    case 'idle':
      if (event.type === 'START') {
        return { ...state, status: 'active', currentStep: 1 }
      }
      break

    case 'active':
      switch (event.type) {
        case 'NEXT':
          return {
            ...state,
            status: 'validating',
            stepData: { ...state.stepData, [state.currentStep]: event.data },
          }
        case 'PREV':
          if (state.currentStep > 1) {
            return { ...state, currentStep: state.currentStep - 1 }
          }
          break
        case 'GOTO':
          if (event.step >= 1 && event.step <= state.currentStep) {
            return { ...state, currentStep: event.step }
          }
          break
      }
      break

    case 'validating':
      switch (event.type) {
        case 'VALIDATE_SUCCESS':
          if (state.currentStep < state.totalSteps) {
            return {
              ...state,
              status: 'active',
              currentStep: state.currentStep + 1,
            }
          }
          return { ...state, status: 'submitting' }
        case 'VALIDATE_ERROR':
          return { ...state, status: 'active', error: event.error }
      }
      break

    case 'submitting':
      switch (event.type) {
        case 'SUBMIT_SUCCESS':
          return { ...state, status: 'complete', error: null }
        case 'SUBMIT_ERROR':
          return { ...state, status: 'error', error: event.error }
      }
      break

    case 'error':
      if (event.type === 'RESET') {
        return {
          status: 'idle',
          currentStep: 0,
          totalSteps: state.totalSteps,
          stepData: {},
          error: null,
        }
      }
      break

    case 'complete':
      if (event.type === 'RESET') {
        return {
          status: 'idle',
          currentStep: 0,
          totalSteps: state.totalSteps,
          stepData: {},
          error: null,
        }
      }
      break
  }

  return state
}

export function useWizardMachine(totalSteps: number) {
  const [state, dispatch] = useReducer(wizardReducer, {
    status: 'idle',
    currentStep: 0,
    totalSteps,
    stepData: {},
    error: null,
  })

  const actions = useMemo(
    () => ({
      start: () => dispatch({ type: 'START' }),
      next: (data: unknown) => dispatch({ type: 'NEXT', data }),
      prev: () => dispatch({ type: 'PREV' }),
      goto: (step: number) => dispatch({ type: 'GOTO', step }),
      validateSuccess: () => dispatch({ type: 'VALIDATE_SUCCESS' }),
      validateError: (error: string) =>
        dispatch({ type: 'VALIDATE_ERROR', error }),
      submit: () => dispatch({ type: 'SUBMIT' }),
      submitSuccess: () => dispatch({ type: 'SUBMIT_SUCCESS' }),
      submitError: (error: string) =>
        dispatch({ type: 'SUBMIT_ERROR', error }),
      reset: () => dispatch({ type: 'RESET' }),
    }),
    []
  )

  return { state, actions }
}
```

---

## 6. Composition Validation

### Ensuring Correct Child Types

```typescript
// src/components/ui/tabs/tabs-validation.tsx
import {
  Children,
  isValidElement,
  type ReactNode,
  type ReactElement,
} from 'react'

// Define component symbols for validation
const TABS_LIST_SYMBOL = Symbol('TabsList')
const TABS_TRIGGER_SYMBOL = Symbol('TabsTrigger')
const TABS_CONTENT_SYMBOL = Symbol('TabsContent')

// Attach symbols to components
interface WithSymbol {
  __tabsSymbol?: symbol
}

export function TabsList({ children }: { children: ReactNode }) {
  return <div role="tablist">{children}</div>
}
(TabsList as WithSymbol).__tabsSymbol = TABS_LIST_SYMBOL

export function TabsTrigger({
  value,
  children,
}: {
  value: string
  children: ReactNode
}) {
  return <button role="tab">{children}</button>
}
(TabsTrigger as WithSymbol).__tabsSymbol = TABS_TRIGGER_SYMBOL

export function TabsContent({
  value,
  children,
}: {
  value: string
  children: ReactNode
}) {
  return <div role="tabpanel">{children}</div>
}
(TabsContent as WithSymbol).__tabsSymbol = TABS_CONTENT_SYMBOL

// Validation function
function validateTabsChildren(children: ReactNode): void {
  let hasTabsList = false
  let hasTabsContent = false
  const triggerValues = new Set<string>()
  const contentValues = new Set<string>()

  Children.forEach(children, (child) => {
    if (!isValidElement(child)) return

    const componentType = child.type as WithSymbol
    const symbol = componentType.__tabsSymbol

    switch (symbol) {
      case TABS_LIST_SYMBOL:
        if (hasTabsList) {
          console.error('[Tabs] Only one TabsList is allowed')
        }
        hasTabsList = true

        // Validate TabsList children
        Children.forEach(child.props.children, (trigger) => {
          if (isValidElement(trigger)) {
            const triggerType = trigger.type as WithSymbol
            if (triggerType.__tabsSymbol !== TABS_TRIGGER_SYMBOL) {
              console.error(
                '[Tabs] TabsList must only contain TabsTrigger components'
              )
            } else {
              const value = (trigger.props as { value: string }).value
              if (triggerValues.has(value)) {
                console.error(
                  `[Tabs] Duplicate TabsTrigger value: "${value}"`
                )
              }
              triggerValues.add(value)
            }
          }
        })
        break

      case TABS_CONTENT_SYMBOL: {
        hasTabsContent = true
        const value = (child.props as { value: string }).value
        if (contentValues.has(value)) {
          console.error(`[Tabs] Duplicate TabsContent value: "${value}"`)
        }
        contentValues.add(value)
        break
      }

      default:
        console.error(
          `[Tabs] Invalid child component. ` +
          `Only TabsList and TabsContent are allowed.`
        )
    }
  })

  // Cross-validation
  if (!hasTabsList) {
    console.error('[Tabs] TabsList is required')
  }

  if (!hasTabsContent) {
    console.error('[Tabs] At least one TabsContent is required')
  }

  // Check trigger/content pairing
  triggerValues.forEach((value) => {
    if (!contentValues.has(value)) {
      console.error(
        `[Tabs] TabsTrigger "${value}" has no matching TabsContent`
      )
    }
  })

  contentValues.forEach((value) => {
    if (!triggerValues.has(value)) {
      console.error(
        `[Tabs] TabsContent "${value}" has no matching TabsTrigger`
      )
    }
  })
}

// Tabs root with validation
interface TabsProps {
  defaultValue?: string
  children: ReactNode
}

export function Tabs({ defaultValue, children }: TabsProps) {
  if (process.env.NODE_ENV === 'development') {
    validateTabsChildren(children)
  }

  return <div data-default-value={defaultValue}>{children}</div>
}
```

### Required vs Optional Parts with TypeScript

```typescript
// src/components/ui/dialog/dialog-types.tsx
import type { ReactNode, ReactElement } from 'react'

// Define the shape of each sub-component's props
interface DialogTriggerProps {
  children: ReactNode
  asChild?: boolean
}

interface DialogContentProps {
  children: ReactNode
  className?: string
}

interface DialogTitleProps {
  children: ReactNode
}

interface DialogDescriptionProps {
  children: ReactNode
}

// Create element types for validation
type DialogTriggerElement = ReactElement<DialogTriggerProps, typeof DialogTrigger>
type DialogContentElement = ReactElement<DialogContentProps, typeof DialogContent>
type DialogTitleElement = ReactElement<DialogTitleProps, typeof DialogTitle>
type DialogDescriptionElement = ReactElement<DialogDescriptionProps, typeof DialogDescription>

// Define required children structure
interface DialogRequiredChildren {
  trigger: DialogTriggerElement
  content: DialogContentElement & {
    props: {
      children: ReactNode & {
        // Content must contain Title for accessibility
        // This is enforced at runtime, hinted at compile time
      }
    }
  }
}

// Placeholder components
declare function DialogTrigger(props: DialogTriggerProps): ReactElement
declare function DialogContent(props: DialogContentProps): ReactElement
declare function DialogTitle(props: DialogTitleProps): ReactElement
declare function DialogDescription(props: DialogDescriptionProps): ReactElement
```

### Runtime Validation with Good Error Messages

```typescript
// src/lib/validation/compound-validation.ts
import { Children, isValidElement, type ReactNode } from 'react'

interface ValidationRule {
  test: (children: ReactNode) => boolean
  message: string
  level: 'error' | 'warning'
}

interface ValidationResult {
  valid: boolean
  errors: string[]
  warnings: string[]
}

export function validateCompoundChildren(
  componentName: string,
  children: ReactNode,
  rules: ValidationRule[]
): ValidationResult {
  const result: ValidationResult = {
    valid: true,
    errors: [],
    warnings: [],
  }

  for (const rule of rules) {
    if (!rule.test(children)) {
      if (rule.level === 'error') {
        result.valid = false
        result.errors.push(`[${componentName}] ${rule.message}`)
      } else {
        result.warnings.push(`[${componentName}] ${rule.message}`)
      }
    }
  }

  if (process.env.NODE_ENV === 'development') {
    result.errors.forEach((e) => console.error(e))
    result.warnings.forEach((w) => console.warn(w))
  }

  return result
}

// Helper functions for common validations
export function hasChildOfType(
  children: ReactNode,
  type: React.ComponentType | string
): boolean {
  let found = false
  Children.forEach(children, (child) => {
    if (isValidElement(child) && child.type === type) {
      found = true
    }
  })
  return found
}

export function countChildrenOfType(
  children: ReactNode,
  type: React.ComponentType | string
): number {
  let count = 0
  Children.forEach(children, (child) => {
    if (isValidElement(child) && child.type === type) {
      count++
    }
  })
  return count
}

export function hasOnlyChildrenOfTypes(
  children: ReactNode,
  types: Array<React.ComponentType | string>
): boolean {
  let valid = true
  Children.forEach(children, (child) => {
    if (isValidElement(child) && !types.includes(child.type as React.ComponentType | string)) {
      valid = false
    }
  })
  return valid
}

// Example usage
const MODAL_VALIDATION_RULES: ValidationRule[] = [
  {
    test: (children) => hasChildOfType(children, ModalContent),
    message: 'Modal must contain a ModalContent component',
    level: 'error',
  },
  {
    test: (children) => countChildrenOfType(children, ModalContent) === 1,
    message: 'Modal should contain exactly one ModalContent',
    level: 'error',
  },
  {
    test: (children) =>
      hasOnlyChildrenOfTypes(children, [ModalContent, ModalTrigger]),
    message: 'Modal should only contain ModalContent and ModalTrigger',
    level: 'warning',
  },
]

// Placeholder component references
declare const ModalContent: React.ComponentType
declare const ModalTrigger: React.ComponentType
```

---

## 7. Forwarding Refs

### Exposing Imperative Handles

```typescript
// src/components/ui/input/input.tsx
import {
  forwardRef,
  useRef,
  useImperativeHandle,
  type ForwardedRef,
} from 'react'
import { cn } from '@/lib/utils'

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  error?: string
  label?: string
}

// Imperative handle interface
export interface InputHandle {
  focus: () => void
  blur: () => void
  clear: () => void
  select: () => void
  getValue: () => string
  setValue: (value: string) => void
}

export const Input = forwardRef(function Input(
  { className, error, label, id, ...props }: InputProps,
  ref: ForwardedRef<InputHandle>
) {
  const inputRef = useRef<HTMLInputElement>(null)

  useImperativeHandle(
    ref,
    () => ({
      focus: () => inputRef.current?.focus(),
      blur: () => inputRef.current?.blur(),
      clear: () => {
        if (inputRef.current) {
          inputRef.current.value = ''
        }
      },
      select: () => inputRef.current?.select(),
      getValue: () => inputRef.current?.value ?? '',
      setValue: (value: string) => {
        if (inputRef.current) {
          inputRef.current.value = value
        }
      },
    }),
    []
  )

  const inputId = id ?? `input-${Math.random().toString(36).slice(2)}`

  return (
    <div className="flex flex-col gap-1.5">
      {label && (
        <label
          htmlFor={inputId}
          className="text-sm font-medium text-slate-700"
        >
          {label}
        </label>
      )}
      <input
        ref={inputRef}
        id={inputId}
        className={cn(
          'px-3 py-2 border rounded-md text-base',
          'focus:outline-none focus:ring-2 focus:ring-primary-500',
          'disabled:opacity-50 disabled:cursor-not-allowed',
          error
            ? 'border-red-500 focus:ring-red-500'
            : 'border-slate-300',
          className
        )}
        aria-invalid={error ? 'true' : undefined}
        aria-describedby={error ? `${inputId}-error` : undefined}
        {...props}
      />
      {error && (
        <span
          id={`${inputId}-error`}
          className="text-sm text-red-500"
          role="alert"
        >
          {error}
        </span>
      )}
    </div>
  )
})
```

### forwardRef with Compound Components

```typescript
// src/components/ui/select/select.tsx
import {
  forwardRef,
  createContext,
  useContext,
  useRef,
  useImperativeHandle,
  useState,
  type ReactNode,
  type ForwardedRef,
} from 'react'

// Context
interface SelectContextValue {
  value: string | null
  setValue: (value: string) => void
  isOpen: boolean
  setIsOpen: (open: boolean) => void
}

const SelectContext = createContext<SelectContextValue | null>(null)

function useSelectContext() {
  const context = useContext(SelectContext)
  if (!context) {
    throw new Error('Select components must be used within a Select')
  }
  return context
}

// Imperative handle
export interface SelectHandle {
  open: () => void
  close: () => void
  toggle: () => void
  getValue: () => string | null
  setValue: (value: string) => void
  focus: () => void
}

// Root component with forwardRef
interface SelectRootProps {
  children: ReactNode
  defaultValue?: string
  onValueChange?: (value: string) => void
}

const SelectRoot = forwardRef(function SelectRoot(
  { children, defaultValue, onValueChange }: SelectRootProps,
  ref: ForwardedRef<SelectHandle>
) {
  const [value, setValueState] = useState<string | null>(defaultValue ?? null)
  const [isOpen, setIsOpen] = useState(false)
  const triggerRef = useRef<HTMLButtonElement>(null)

  const setValue = (newValue: string) => {
    setValueState(newValue)
    onValueChange?.(newValue)
  }

  useImperativeHandle(
    ref,
    () => ({
      open: () => setIsOpen(true),
      close: () => setIsOpen(false),
      toggle: () => setIsOpen((prev) => !prev),
      getValue: () => value,
      setValue,
      focus: () => triggerRef.current?.focus(),
    }),
    [value]
  )

  return (
    <SelectContext.Provider value={{ value, setValue, isOpen, setIsOpen }}>
      <div className="relative inline-block">{children}</div>
    </SelectContext.Provider>
  )
})

// Trigger component (also needs ref forwarding)
interface SelectTriggerProps {
  children?: ReactNode
  className?: string
  placeholder?: string
}

const SelectTrigger = forwardRef<HTMLButtonElement, SelectTriggerProps>(
  function SelectTrigger({ children, className, placeholder }, ref) {
    const { value, isOpen, setIsOpen } = useSelectContext()

    return (
      <button
        ref={ref}
        type="button"
        role="combobox"
        aria-expanded={isOpen}
        aria-haspopup="listbox"
        onClick={() => setIsOpen(!isOpen)}
        className={cn(
          'flex items-center justify-between w-full px-3 py-2',
          'border border-slate-300 rounded-md bg-white',
          'focus:outline-none focus:ring-2 focus:ring-primary-500',
          className
        )}
      >
        <span className={value ? '' : 'text-slate-400'}>
          {children ?? value ?? placeholder ?? 'Select...'}
        </span>
        <svg
          className={cn(
            'w-4 h-4 transition-transform',
            isOpen && 'rotate-180'
          )}
          fill="none"
          viewBox="0 0 24 24"
          stroke="currentColor"
        >
          <path
            strokeLinecap="round"
            strokeLinejoin="round"
            strokeWidth={2}
            d="M19 9l-7 7-7-7"
          />
        </svg>
      </button>
    )
  }
)

// Content and Item components
interface SelectContentProps {
  children: ReactNode
  className?: string
}

function SelectContent({ children, className }: SelectContentProps) {
  const { isOpen } = useSelectContext()

  if (!isOpen) return null

  return (
    <div
      role="listbox"
      className={cn(
        'absolute z-50 w-full mt-1',
        'bg-white border border-slate-200 rounded-md shadow-lg',
        'max-h-60 overflow-auto',
        className
      )}
    >
      {children}
    </div>
  )
}

interface SelectItemProps {
  value: string
  children: ReactNode
  className?: string
}

function SelectItem({ value, children, className }: SelectItemProps) {
  const { value: selectedValue, setValue, setIsOpen } = useSelectContext()
  const isSelected = value === selectedValue

  return (
    <div
      role="option"
      aria-selected={isSelected}
      onClick={() => {
        setValue(value)
        setIsOpen(false)
      }}
      className={cn(
        'px-3 py-2 cursor-pointer',
        'hover:bg-slate-100',
        isSelected && 'bg-primary-50 text-primary-700',
        className
      )}
    >
      {children}
    </div>
  )
}

// Assemble compound component
export const Select = Object.assign(SelectRoot, {
  Trigger: SelectTrigger,
  Content: SelectContent,
  Item: SelectItem,
})
```

### Consumer Usage with Refs

```tsx
import { useRef } from 'react'
import { Select, type SelectHandle } from '@/components/ui/select'
import { Button } from '@/components/ui/button'

export function ControlledSelect() {
  const selectRef = useRef<SelectHandle>(null)

  const handleReset = () => {
    selectRef.current?.setValue('option1')
    selectRef.current?.close()
  }

  const handleFocusSelect = () => {
    selectRef.current?.focus()
    selectRef.current?.open()
  }

  return (
    <div className="flex flex-col gap-4">
      <Select ref={selectRef} defaultValue="option1">
        <Select.Trigger placeholder="Choose an option" />
        <Select.Content>
          <Select.Item value="option1">Option 1</Select.Item>
          <Select.Item value="option2">Option 2</Select.Item>
          <Select.Item value="option3">Option 3</Select.Item>
        </Select.Content>
      </Select>

      <div className="flex gap-2">
        <Button variant="outline" onClick={handleReset}>
          Reset
        </Button>
        <Button variant="outline" onClick={handleFocusSelect}>
          Open Select
        </Button>
      </div>
    </div>
  )
}
```

---

## 8. Testing Strategies

### Testing Compound Components

```typescript
// src/components/ui/accordion/__tests__/accordion.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Accordion } from '../accordion'

describe('Accordion', () => {
  const defaultItems = [
    { value: 'item-1', trigger: 'Section 1', content: 'Content 1' },
    { value: 'item-2', trigger: 'Section 2', content: 'Content 2' },
    { value: 'item-3', trigger: 'Section 3', content: 'Content 3' },
  ]

  const renderAccordion = (props = {}) => {
    return render(
      <Accordion type="single" collapsible {...props}>
        {defaultItems.map((item) => (
          <Accordion.Item key={item.value} value={item.value}>
            <Accordion.Trigger value={item.value}>
              {item.trigger}
            </Accordion.Trigger>
            <Accordion.Content value={item.value}>
              {item.content}
            </Accordion.Content>
          </Accordion.Item>
        ))}
      </Accordion>
    )
  }

  describe('rendering', () => {
    it('renders all items collapsed by default', () => {
      renderAccordion()

      defaultItems.forEach((item) => {
        expect(screen.getByText(item.trigger)).toBeInTheDocument()
        // Content exists but is hidden
        expect(screen.getByText(item.content)).toHaveAttribute('hidden')
      })
    })

    it('renders with defaultValue expanded', () => {
      renderAccordion({ defaultValue: 'item-2' })

      expect(screen.getByText('Content 1')).toHaveAttribute('hidden')
      expect(screen.getByText('Content 2')).not.toHaveAttribute('hidden')
      expect(screen.getByText('Content 3')).toHaveAttribute('hidden')
    })
  })

  describe('single type behavior', () => {
    it('expands item on click', async () => {
      const user = userEvent.setup()
      renderAccordion()

      await user.click(screen.getByText('Section 1'))

      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')
    })

    it('collapses other items when opening a new one', async () => {
      const user = userEvent.setup()
      renderAccordion({ defaultValue: 'item-1' })

      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')

      await user.click(screen.getByText('Section 2'))

      expect(screen.getByText('Content 1')).toHaveAttribute('hidden')
      expect(screen.getByText('Content 2')).not.toHaveAttribute('hidden')
    })

    it('allows collapsing when collapsible is true', async () => {
      const user = userEvent.setup()
      renderAccordion({ collapsible: true, defaultValue: 'item-1' })

      await user.click(screen.getByText('Section 1'))

      expect(screen.getByText('Content 1')).toHaveAttribute('hidden')
    })

    it('prevents collapsing when collapsible is false', async () => {
      const user = userEvent.setup()
      renderAccordion({ collapsible: false, defaultValue: 'item-1' })

      await user.click(screen.getByText('Section 1'))

      // Should still be visible
      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')
    })
  })

  describe('multiple type behavior', () => {
    it('allows multiple items to be open', async () => {
      const user = userEvent.setup()
      renderAccordion({ type: 'multiple' })

      await user.click(screen.getByText('Section 1'))
      await user.click(screen.getByText('Section 2'))

      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')
      expect(screen.getByText('Content 2')).not.toHaveAttribute('hidden')
    })
  })

  describe('accessibility', () => {
    it('has correct ARIA attributes', () => {
      renderAccordion({ defaultValue: 'item-1' })

      const trigger1 = screen.getByText('Section 1')
      const trigger2 = screen.getByText('Section 2')

      expect(trigger1).toHaveAttribute('aria-expanded', 'true')
      expect(trigger2).toHaveAttribute('aria-expanded', 'false')
      expect(trigger1).toHaveAttribute('aria-controls')
    })

    it('supports keyboard navigation', async () => {
      const user = userEvent.setup()
      renderAccordion()

      const trigger = screen.getByText('Section 1')
      trigger.focus()

      await user.keyboard('{Enter}')
      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')

      await user.keyboard('{Enter}')
      expect(screen.getByText('Content 1')).toHaveAttribute('hidden')

      await user.keyboard(' ')
      expect(screen.getByText('Content 1')).not.toHaveAttribute('hidden')
    })
  })
})
```

### Mocking Context for Unit Tests

```typescript
// src/components/ui/modal/__tests__/modal-body.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { ModalContext, type ModalContextValue } from '../modal-context'
import { ModalBody } from '../modal-body'
import { ModalHeader } from '../modal-header'

// Create mock context wrapper
function createMockModalContext(
  overrides: Partial<ModalContextValue> = {}
): ModalContextValue {
  return {
    isOpen: true,
    onClose: vi.fn(),
    ...overrides,
  }
}

function renderWithModalContext(
  ui: React.ReactElement,
  contextValue: ModalContextValue
) {
  return render(
    <ModalContext.Provider value={contextValue}>
      {ui}
    </ModalContext.Provider>
  )
}

describe('ModalBody', () => {
  it('renders children', () => {
    const context = createMockModalContext()

    renderWithModalContext(
      <ModalBody>Test content</ModalBody>,
      context
    )

    expect(screen.getByText('Test content')).toBeInTheDocument()
  })

  it('applies custom className', () => {
    const context = createMockModalContext()

    renderWithModalContext(
      <ModalBody className="custom-class">Content</ModalBody>,
      context
    )

    expect(screen.getByText('Content').parentElement).toHaveClass('custom-class')
  })
})

describe('ModalHeader', () => {
  it('calls onClose when close button clicked', () => {
    const onClose = vi.fn()
    const context = createMockModalContext({ onClose })

    renderWithModalContext(
      <ModalHeader>Title</ModalHeader>,
      context
    )

    fireEvent.click(screen.getByRole('button', { name: /close/i }))

    expect(onClose).toHaveBeenCalledTimes(1)
  })

  it('hides close button when showClose is false', () => {
    const context = createMockModalContext()

    renderWithModalContext(
      <ModalHeader showClose={false}>Title</ModalHeader>,
      context
    )

    expect(screen.queryByRole('button', { name: /close/i })).not.toBeInTheDocument()
  })
})
```

### Testing Composition Constraints

```typescript
// src/components/ui/tabs/__tests__/tabs-validation.test.tsx
import { render } from '@testing-library/react'
import { Tabs } from '../tabs'

describe('Tabs composition validation', () => {
  const consoleSpy = vi.spyOn(console, 'error').mockImplementation(() => {})

  afterEach(() => {
    consoleSpy.mockClear()
  })

  afterAll(() => {
    consoleSpy.mockRestore()
  })

  it('warns when TabsList is missing', () => {
    render(
      <Tabs defaultValue="tab1">
        <Tabs.Content value="tab1">Content</Tabs.Content>
      </Tabs>
    )

    expect(consoleSpy).toHaveBeenCalledWith(
      expect.stringContaining('TabsList is required')
    )
  })

  it('warns when TabsContent is missing', () => {
    render(
      <Tabs defaultValue="tab1">
        <Tabs.List>
          <Tabs.Trigger value="tab1">Tab 1</Tabs.Trigger>
        </Tabs.List>
      </Tabs>
    )

    expect(consoleSpy).toHaveBeenCalledWith(
      expect.stringContaining('at least one TabsContent is required')
    )
  })

  it('warns when trigger has no matching content', () => {
    render(
      <Tabs defaultValue="tab1">
        <Tabs.List>
          <Tabs.Trigger value="tab1">Tab 1</Tabs.Trigger>
          <Tabs.Trigger value="tab2">Tab 2</Tabs.Trigger>
        </Tabs.List>
        <Tabs.Content value="tab1">Content 1</Tabs.Content>
        {/* Missing tab2 content */}
      </Tabs>
    )

    expect(consoleSpy).toHaveBeenCalledWith(
      expect.stringContaining('no matching TabsContent')
    )
  })

  it('warns on duplicate values', () => {
    render(
      <Tabs defaultValue="tab1">
        <Tabs.List>
          <Tabs.Trigger value="tab1">Tab 1</Tabs.Trigger>
          <Tabs.Trigger value="tab1">Tab 1 Duplicate</Tabs.Trigger>
        </Tabs.List>
        <Tabs.Content value="tab1">Content</Tabs.Content>
      </Tabs>
    )

    expect(consoleSpy).toHaveBeenCalledWith(
      expect.stringContaining('Duplicate')
    )
  })

  it('does not warn for valid composition', () => {
    render(
      <Tabs defaultValue="tab1">
        <Tabs.List>
          <Tabs.Trigger value="tab1">Tab 1</Tabs.Trigger>
          <Tabs.Trigger value="tab2">Tab 2</Tabs.Trigger>
        </Tabs.List>
        <Tabs.Content value="tab1">Content 1</Tabs.Content>
        <Tabs.Content value="tab2">Content 2</Tabs.Content>
      </Tabs>
    )

    expect(consoleSpy).not.toHaveBeenCalled()
  })
})
```

### Testing Headless Hook

```typescript
// src/components/ui/accordion/__tests__/use-accordion.test.ts
import { renderHook, act } from '@testing-library/react'
import { useAccordion } from '../use-accordion'

describe('useAccordion', () => {
  describe('single type', () => {
    it('initializes with empty expanded set', () => {
      const { result } = renderHook(() => useAccordion({ type: 'single' }))

      expect(result.current.expandedItems.size).toBe(0)
    })

    it('initializes with defaultValue', () => {
      const { result } = renderHook(() =>
        useAccordion({ type: 'single', defaultValue: 'item-1' })
      )

      expect(result.current.isExpanded('item-1')).toBe(true)
    })

    it('toggles items correctly', () => {
      const { result } = renderHook(() => useAccordion({ type: 'single' }))

      act(() => {
        result.current.toggle('item-1')
      })

      expect(result.current.isExpanded('item-1')).toBe(true)

      act(() => {
        result.current.toggle('item-2')
      })

      expect(result.current.isExpanded('item-1')).toBe(false)
      expect(result.current.isExpanded('item-2')).toBe(true)
    })

    it('respects collapsible option', () => {
      const { result } = renderHook(() =>
        useAccordion({
          type: 'single',
          defaultValue: 'item-1',
          collapsible: false,
        })
      )

      act(() => {
        result.current.toggle('item-1')
      })

      // Should still be expanded because collapsible is false
      expect(result.current.isExpanded('item-1')).toBe(true)
    })
  })

  describe('multiple type', () => {
    it('allows multiple items expanded', () => {
      const { result } = renderHook(() => useAccordion({ type: 'multiple' }))

      act(() => {
        result.current.expand('item-1')
        result.current.expand('item-2')
      })

      expect(result.current.isExpanded('item-1')).toBe(true)
      expect(result.current.isExpanded('item-2')).toBe(true)
    })

    it('expandAll works correctly', () => {
      const { result } = renderHook(() => useAccordion({ type: 'multiple' }))

      act(() => {
        result.current.expandAll(['item-1', 'item-2', 'item-3'])
      })

      expect(result.current.expandedItems.size).toBe(3)
    })

    it('collapseAll works correctly', () => {
      const { result } = renderHook(() =>
        useAccordion({
          type: 'multiple',
          defaultValue: ['item-1', 'item-2'],
        })
      )

      act(() => {
        result.current.collapseAll()
      })

      expect(result.current.expandedItems.size).toBe(0)
    })
  })

  describe('prop getters', () => {
    it('getItemProps returns correct state', () => {
      const { result } = renderHook(() =>
        useAccordion({ type: 'single', defaultValue: 'item-1' })
      )

      const props = result.current.getItemProps('item-1')

      expect(props['data-state']).toBe('open')
      expect(props['aria-expanded']).toBe(true)
    })

    it('getTriggerProps includes click handler', () => {
      const { result } = renderHook(() => useAccordion({ type: 'single' }))

      const props = result.current.getTriggerProps('item-1')

      expect(props.onClick).toBeDefined()
      expect(props['aria-controls']).toBe('accordion-content-item-1')
    })

    it('getContentProps includes correct id', () => {
      const { result } = renderHook(() => useAccordion({ type: 'single' }))

      const props = result.current.getContentProps('item-1')

      expect(props.id).toBe('accordion-content-item-1')
      expect(props.hidden).toBe(true)
    })
  })
})
```

---

## Related Documentation

- [SKILL.md](./SKILL.md) - Basic compound component pattern
- [LAYER-02-COMPONENTS.md](../../system/LAYER-02-COMPONENTS.md) - Component inventory
- [APPENDIX-C-RADIX-PRIMITIVES.md](../../system/APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md) - Radix UI reference
- [LAYER-08-ACCESSIBILITY.md](../../system/LAYER-08-ACCESSIBILITY.md) - Accessibility requirements
