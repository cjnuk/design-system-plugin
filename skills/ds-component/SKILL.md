---
skill: ds-component
description: Generate a new UI component following design system patterns with cva, TypeScript types, and accessibility
triggers:
  - "create component"
  - "new component"
  - "generate component"
  - "ds component"
  - "add a component"
arguments:
  - name: component_name
    description: Name of the component to create (e.g., Badge, StatusIndicator)
    required: true
---

# Create New Component

Generate a UI component following the design system's patterns: shadcn/ui conventions, cva for variants, semantic tokens, TypeScript types, and WCAG 2.1 AA accessibility.

## Context Resolution

Before executing, check for project-specific configuration:

1. Read `.claude/design-system/project-config.yaml` for project metadata
2. Read `.claude/design-system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md` for project patterns
3. Fall back to `${CLAUDE_PLUGIN_ROOT}/system/` layer files for defaults

## Component Template

When creating `{component_name}`, follow this structure:

```typescript
// components/ui/{component-name}.tsx
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const {componentName}Variants = cva(
  // Base classes (always applied)
  "inline-flex items-center justify-center",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        secondary: "bg-secondary text-secondary-foreground",
        destructive: "bg-destructive text-destructive-foreground",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
        ghost: "hover:bg-accent hover:text-accent-foreground",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 px-3",
        lg: "h-11 px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

export interface {ComponentName}Props
  extends React.HTMLAttributes<HTMLElement>,
    VariantProps<typeof {componentName}Variants> {
  // Add component-specific props here
}

const {ComponentName} = React.forwardRef<
  HTMLElement,
  {ComponentName}Props
>(({ className, variant, size, ...props }, ref) => {
  return (
    <element
      className={cn({componentName}Variants({ variant, size }), className)}
      ref={ref}
      {...props}
    />
  )
})
{ComponentName}.displayName = "{ComponentName}"

export { {ComponentName}, {componentName}Variants }
```

## Mandatory Requirements

### 1. Semantic Tokens Only

```typescript
// NEVER hardcode colors
className="bg-slate-900 text-white"

// ALWAYS use semantic tokens
className="bg-primary text-primary-foreground"
```

### 2. Use cva for Variants

```typescript
// NEVER use conditional classes
className={`btn ${isPrimary ? 'bg-blue-500' : 'bg-gray-500'}`}

// ALWAYS use cva
const variants = cva("base", {
  variants: {
    variant: { primary: "bg-primary", secondary: "bg-secondary" }
  }
})
```

### 3. Export TypeScript Types

```typescript
// ALWAYS export the props interface
export interface BadgeProps
  extends React.HTMLAttributes<HTMLSpanElement>,
    VariantProps<typeof badgeVariants> {}
```

### 4. Accessibility Requirements

- Interactive elements must be keyboard accessible
- Add `aria-label` for icon-only buttons
- Ensure 4.5:1 color contrast ratio
- Use semantic HTML elements
- Support `prefers-reduced-motion`

```typescript
// Icon button with accessibility
<button
  type="button"
  aria-label="Close dialog"
  className={cn(buttonVariants({ size: "icon" }))}
>
  <X className="h-4 w-4" />
</button>
```

### 5. File Naming Convention

```
components/ui/{component-name}.tsx  // kebab-case file
ComponentName                        // PascalCase export
componentVariants                    // camelCase for variants
```

## Example: Badge Component

```typescript
// components/ui/badge.tsx
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const badgeVariants = cva(
  "inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2",
  {
    variants: {
      variant: {
        default: "border-transparent bg-primary text-primary-foreground",
        secondary: "border-transparent bg-secondary text-secondary-foreground",
        destructive: "border-transparent bg-destructive text-destructive-foreground",
        outline: "text-foreground",
        success: "border-transparent bg-green-100 text-green-800 dark:bg-green-900 dark:text-green-100",
        warning: "border-transparent bg-amber-100 text-amber-800 dark:bg-amber-900 dark:text-amber-100",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
)

export interface BadgeProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof badgeVariants> {}

function Badge({ className, variant, ...props }: BadgeProps) {
  return (
    <div className={cn(badgeVariants({ variant }), className)} {...props} />
  )
}

export { Badge, badgeVariants }
```

## Validation Checklist

Before completing, verify:

- [ ] Uses semantic tokens (no hardcoded colors like `bg-slate-900`)
- [ ] Implements cva for variants
- [ ] Exports TypeScript interface for props
- [ ] Uses `VariantProps<typeof componentVariants>` for variant types
- [ ] No `any` types
- [ ] Keyboard accessible (Tab, Enter, Space, Escape work correctly)
- [ ] Has `aria-label` if icon-only
- [ ] File named in kebab-case
- [ ] Component named in PascalCase
- [ ] Uses `cn()` for class merging
- [ ] Forwards ref with `React.forwardRef`

## Token Reference

| Token | Usage |
|-------|-------|
| `bg-primary` | Primary buttons, links |
| `bg-secondary` | Secondary actions |
| `bg-destructive` | Danger/delete actions |
| `bg-muted` | Subtle backgrounds |
| `text-foreground` | Primary text |
| `text-muted-foreground` | Secondary text |
| `border-border` | Default borders |
| `ring-ring` | Focus rings |
