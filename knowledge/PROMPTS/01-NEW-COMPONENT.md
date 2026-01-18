---
title: "Prompt: Create New Component"
task_type: "component-creation"
retrieval_path: ["LLM-AGENT-GUIDE", "LAYER-01-DESIGN-TOKENS", "LAYER-02-COMPONENTS", "LAYER-08-ACCESSIBILITY"]
---

# PROMPT: CREATE NEW COMPONENT

Use this prompt when asking an LLM to create a new UI component following this design system.

---

## PROMPT TEMPLATE

```
Create a [COMPONENT_NAME] component following our design system specifications.

Requirements:
- [List specific requirements]

Technical constraints:
- Use shadcn/ui patterns (Radix primitives + Tailwind)
- Apply semantic tokens from LAYER-01-DESIGN-TOKENS
- Support variants via class-variance-authority (cva)
- Ensure WCAG 2.1 AA accessibility
- Export from components/ui/[component-name].tsx

Reference files:
- system/LAYER-01-DESIGN-TOKENS.md for color/spacing tokens
- system/LAYER-02-COMPONENTS.md for component anatomy and variant patterns
- system/LAYER-08-ACCESSIBILITY.md for a11y requirements
```

---

## EXAMPLE USAGE

```
Create a Badge component following our design system specifications.

Requirements:
- Display short status text or labels
- Support variants: default, secondary, destructive, outline
- Support sizes: sm, default, lg
- Optionally show a dot indicator

Technical constraints:
- Use shadcn/ui patterns (Radix primitives + Tailwind)
- Apply semantic tokens from LAYER-01-DESIGN-TOKENS
- Support variants via class-variance-authority (cva)
- Ensure WCAG 2.1 AA accessibility
- Export from components/ui/badge.tsx
```

---

## EXPECTED OUTPUT STRUCTURE

```typescript
// components/ui/[component].tsx
import * as React from "react"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

const componentVariants = cva(
  "base-classes",
  {
    variants: {
      variant: { /* ... */ },
      size: { /* ... */ },
    },
    defaultVariants: { /* ... */ },
  }
)

export interface ComponentProps
  extends React.HTMLAttributes<HTMLElement>,
    VariantProps<typeof componentVariants> {}

export function Component({ className, variant, size, ...props }: ComponentProps) {
  return (
    <element
      className={cn(componentVariants({ variant, size }), className)}
      {...props}
    />
  )
}
```

---

## VALIDATION CHECKLIST

- [ ] Uses semantic tokens (not hardcoded colors)
- [ ] Implements cva for variants
- [ ] Includes TypeScript types
- [ ] Meets WCAG 2.1 AA
- [ ] Follows file naming convention
- [ ] Exports component properly
