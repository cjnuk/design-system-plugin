# Implementation Patterns

Project-specific implementation guidelines and patterns.

## Component Creation Workflow

### 1. Plan the Component

Before creating, ask:
- What is the component's responsibility?
- What props does it need?
- What variants or states does it have?
- What accessibility requirements apply?

### 2. Follow the Template

Use `/ds-component` skill which generates:

```typescript
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const componentVariants = cva(
  // Base classes
  "base classes here",
  {
    variants: {
      variant: {
        default: "...",
        secondary: "...",
      },
    },
  }
)

export interface ComponentProps
  extends React.HTMLAttributes<HTMLElement>,
    VariantProps<typeof componentVariants> {}

const Component = React.forwardRef<
  HTMLElement,
  ComponentProps
>(({ className, variant, ...props }, ref) => {
  return (
    <element
      className={cn(componentVariants({ variant }), className)}
      ref={ref}
      {...props}
    />
  )
})
Component.displayName = "Component"

export { Component, componentVariants }
```

### 3. Mandatory Requirements

#### Semantic Tokens Only

```typescript
// ❌ NEVER hardcode colors
className="bg-slate-900 text-white"

// ✅ ALWAYS use semantic tokens
className="bg-primary text-primary-foreground"
```

**Token Reference**:
- `bg-primary` / `text-primary-foreground`: Primary actions
- `bg-secondary` / `text-secondary-foreground`: Secondary actions
- `bg-destructive` / `text-destructive-foreground`: Danger/delete
- `bg-muted` / `text-muted-foreground`: Subtle, disabled
- `border-border`: Default borders
- `ring-ring`: Focus rings

#### CVA for Variants

```typescript
// ❌ NEVER conditional classes
className={`btn ${isPrimary ? 'bg-blue-500' : 'bg-gray-500'}`}

// ✅ ALWAYS use CVA
const variants = cva("base", {
  variants: {
    variant: { primary: "bg-primary", secondary: "bg-secondary" }
  }
})
```

#### TypeScript Strict Types

```typescript
// ✅ Export the props interface
export interface BadgeProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof badgeVariants> {}

// ✅ Use VariantProps for type safety
<Badge variant="success" size="lg" />
```

#### Accessibility (WCAG AA)

- Interactive elements: keyboard accessible (Tab, Enter, Space, Escape)
- Icon-only buttons: `aria-label`
- Color contrast: 4.5:1 minimum for text
- Semantic HTML: `<button>` not `<div role="button">`
- Focus visible: Always visible outline/indicator
- Motion: Respect `prefers-reduced-motion`

```typescript
// ✅ Icon button with accessibility
<button
  type="button"
  aria-label="Close dialog"
  className={cn(buttonVariants({ size: "icon" }))}
>
  <X className="h-4 w-4" />
</button>
```

#### File Naming Convention

```
components/ui/{component-name}.tsx     # kebab-case file
ComponentName                          # PascalCase export
componentVariants                      # camelCase for variants
```

### 4. Validation Checklist

Before committing:

- [ ] Uses semantic tokens (no hardcoded colors)
- [ ] Implements CVA for variants
- [ ] Exports TypeScript interface for props
- [ ] No `any` types
- [ ] Keyboard accessible
- [ ] Has `aria-label` if icon-only
- [ ] File named in kebab-case
- [ ] Component named in PascalCase
- [ ] Uses `cn()` for class merging
- [ ] Forwards ref with `React.forwardRef`

## State Management Patterns

### Context + Hooks

For simple local state:
```typescript
const MyContext = createContext<MyContextType | undefined>(undefined)

export function MyProvider({ children }: { children: React.ReactNode }) {
  const [state, setState] = useState(initialState)

  return (
    <MyContext.Provider value={{ state, setState }}>
      {children}
    </MyContext.Provider>
  )
}

export function useMyContext() {
  const context = useContext(MyContext)
  if (!context) throw new Error("useMyContext must be used within MyProvider")
  return context
}
```

### Jotai Atoms

For global state:
```typescript
import { atom, useAtom } from 'jotai'

export const userAtom = atom<User | null>(null)
export const isLoadingAtom = atom(false)

// In component:
const [user, setUser] = useAtom(userAtom)
```

## Form Patterns

### Using react-hook-form

```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
})

type FormData = z.infer<typeof schema>

export function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm<FormData>({
    resolver: zodResolver(schema),
  })

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
    </form>
  )
}
```

## Data Fetching

### Using TanStack Query (React Query)

```typescript
import { useQuery, useMutation } from '@tanstack/react-query'

export function useUser(userId: string) {
  return useQuery({
    queryKey: ['users', userId],
    queryFn: async () => {
      const res = await fetch(`/api/users/${userId}`)
      return res.json()
    },
  })
}

export function useCreateUser() {
  return useMutation({
    mutationFn: async (data: CreateUserInput) => {
      const res = await fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(data),
      })
      return res.json()
    },
  })
}
```

## Error Handling

### User-Facing Errors

Use semantic tokens and accessible patterns:

```typescript
<div className="rounded-md bg-destructive/10 p-4" role="alert">
  <p className="text-sm font-medium text-destructive">Error</p>
  <p className="text-sm text-destructive/80">{error.message}</p>
</div>
```

## Performance Considerations

### Code Splitting

Use dynamic imports for route-based code splitting:

```typescript
const Admin = dynamic(() => import('@/pages/admin'), {
  loading: () => <Skeleton />,
})
```

### Memoization

Use `React.memo` sparingly, only for expensive renders:

```typescript
export const MemoizedComponent = React.memo(Component, (prev, next) => {
  return prev.id === next.id // return true if props are equal
})
```

### Image Optimization

Use `next/image` or equivalent:

```typescript
import Image from 'next/image'

<Image
  src="/image.png"
  alt="Description"
  width={800}
  height={600}
  priority={false}
/>
```

## Common Pitfalls to Avoid

1. **Conditional Rendering with Falsy Values**
   ```typescript
   // ❌ Bug: renders nothing if count is 0
   {count && <Component />}

   // ✅ Correct
   {count > 0 && <Component />}
   ```

2. **Missing Aria Labels**
   ```typescript
   // ❌ Inaccessible icon button
   <button><Icon /></button>

   // ✅ Accessible
   <button aria-label="Close"><Icon /></button>
   ```

3. **Hardcoded Responsive Classes**
   ```typescript
   // ❌ Not maintainable
   className="text-sm md:text-base lg:text-lg"

   // ✅ Use CVA or consistent system
   className={textVariants({ size })}
   ```

4. **Missing TypeScript Types**
   ```typescript
   // ❌ Using any
   const handleClick = (e: any) => {}

   // ✅ Type properly
   const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {}
   ```

---

See LAYER-02-COMPONENTS.md for detailed component patterns and LAYER-08-ACCESSIBILITY.md for a11y requirements.
