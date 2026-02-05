---
skill: ds-review
description: Review code against design system standards for tokens, accessibility, TypeScript, and patterns
triggers:
  - "review code"
  - "code review"
  - "ds review"
  - "check design system"
  - "validate against design system"
arguments:
  - name: file_path
    description: Path to the file or component to review
    required: false
---

# Design System Code Review

Review UI code for compliance with the design system. Checks semantic token usage, component patterns, accessibility, TypeScript types, and naming conventions.

## Context Resolution

Before executing, check for project-specific configuration:

1. Read `.claude/design-system/project-config.yaml` for project metadata
2. Read `.claude/design-system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md` for project patterns
3. Fall back to `${CLAUDE_PLUGIN_ROOT}/system/` layer files for defaults

## Review Criteria

### 1. Semantic Token Usage

**Check for hardcoded colors:**

```typescript
// BAD - Hardcoded
className="bg-slate-900 text-white border-gray-200"
className="bg-blue-500 hover:bg-blue-600"
style={{ color: '#1e293b' }}

// GOOD - Semantic tokens
className="bg-primary text-primary-foreground border-border"
className="bg-background text-foreground"
```

**Token mapping reference:**

| Hardcoded | Semantic Token |
|-----------|----------------|
| `bg-white` | `bg-background` |
| `bg-slate-900` | `bg-background` (dark) |
| `text-slate-900` | `text-foreground` |
| `text-slate-500` | `text-muted-foreground` |
| `border-slate-200` | `border-border` |
| `bg-blue-500` | `bg-primary` |
| `text-blue-500` | `text-primary` |
| `bg-red-500` | `bg-destructive` |

### 2. Component Composition

**Check for proper patterns:**

```typescript
// BAD - Custom implementation when shadcn/ui exists
const CustomButton = styled.button`...`
<div onClick={handleClick} className="cursor-pointer">

// GOOD - Using shadcn/ui components
import { Button } from '@/components/ui/button'
<Button variant="default" onClick={handleClick}>

// BAD - Prop drilling / monolithic
<CustomButton color="blue" size="large" rounded loading disabled>

// GOOD - Composable with variants
<Button variant="default" size="lg" disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Loading
</Button>
```

### 3. Variant Implementation

**Check for cva usage:**

```typescript
// BAD - Inline conditional classes
className={`btn ${isPrimary ? 'bg-blue-500' : 'bg-gray-500'}`}
className={cn("base", primary && "bg-blue-500", secondary && "bg-gray-500")}

// GOOD - Using cva
const buttonVariants = cva("base-classes", {
  variants: {
    variant: {
      primary: "bg-primary text-primary-foreground",
      secondary: "bg-secondary text-secondary-foreground",
    }
  }
})
```

### 4. Accessibility

**Check for:**

```typescript
// BAD
<div onClick={handleClick}>Click me</div>
<button><X /></button>
<img src="/hero.jpg" />
<input placeholder="Email" />

// GOOD
<button type="button" onClick={handleClick}>Click me</button>
<button aria-label="Close dialog"><X className="h-4 w-4" /></button>
<img src="/hero.jpg" alt="Team collaborating" />
<label htmlFor="email">Email</label>
<input id="email" type="email" placeholder="you@example.com" />
```

### 5. TypeScript Types

**Check for:**

```typescript
// BAD - Any types
function Component(props: any) { }
const handleClick = (e: any) => { }

// GOOD - Proper types
interface ComponentProps {
  title: string
  onAction: () => void
}

function Component({ title, onAction }: ComponentProps) { }
const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => { }

// GOOD - Variant types from cva
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}
```

### 6. Naming Conventions

**Check for:**

```typescript
// BAD - Inconsistent naming
const MyBtn = () => {}           // Abbreviated
const my_input = () => {}        // Snake case
const SUBMIT_BUTTON = () => {}   // All caps

// GOOD - PascalCase components
const Button = () => {}
const TextInput = () => {}
const SubmitButton = () => {}

// GOOD - camelCase for variants/functions
const buttonVariants = cva(...)
const handleSubmit = () => {}
```

## Review Output Format

```markdown
## Code Review Summary

### Compliant
- [List things done well]

### Suggestions
- [List minor improvements]

### Issues
- [List violations that must be fixed]

### Code Changes
[Provide specific code fixes for each issue]
```

## Example Review

```markdown
## Code Review Summary

### Compliant
- Proper TypeScript types on props
- Using shadcn/ui Button component
- Logical component composition
- Using cn() for class merging

### Suggestions
- Consider extracting repeated className patterns into cva variants
- Add data-testid for easier testing
- Add JSDoc comment for complex prop types

### Issues

1. **Line 15**: Hardcoded color `bg-blue-500` should use `bg-primary`
   ```diff
   - className="bg-blue-500"
   + className="bg-primary"
   ```

2. **Line 23**: Icon button missing aria-label
   ```diff
   - <Button size="icon"><X /></Button>
   + <Button size="icon" aria-label="Close"><X /></Button>
   ```

3. **Line 31**: Using `any` type for event handler
   ```diff
   - const handleChange = (e: any) => {
   + const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
   ```

4. **Line 45**: Clickable div should be a button
   ```diff
   - <div onClick={handleClick} className="cursor-pointer">
   + <button type="button" onClick={handleClick}>
   ```
```

## Automated Checks

Run these commands to catch issues:

```bash
# TypeScript type checking
npx tsc --noEmit

# Lint for patterns
npx eslint src/components --ext .tsx

# Accessibility testing
npx jest --testPathPattern=a11y
```

## Review Checklist

### Tokens
- [ ] No hardcoded colors
- [ ] Using semantic tokens (`bg-primary`, not `bg-blue-500`)
- [ ] Dark mode works (tokens handle both)

### Components
- [ ] Using shadcn/ui where available
- [ ] Variants implemented with cva
- [ ] Using `cn()` for class merging
- [ ] Forwarding refs where needed

### Accessibility
- [ ] Interactive elements are buttons/links
- [ ] Icon buttons have aria-label
- [ ] Images have alt text
- [ ] Form inputs have labels
- [ ] Keyboard navigation works

### TypeScript
- [ ] No `any` types
- [ ] Props interface exported
- [ ] Using `VariantProps` for cva types
- [ ] Event handlers properly typed

### Naming
- [ ] Components are PascalCase
- [ ] Functions are camelCase
- [ ] Files are kebab-case
- [ ] Variants use camelCase
