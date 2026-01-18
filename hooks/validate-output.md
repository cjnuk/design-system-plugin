---
hook: validate-design-system
description: Validates generated UI code against design system rules
events:
  - PostToolUse
match_tools:
  - Write
  - Edit
match_files:
  - "**/*.tsx"
  - "**/*.jsx"
---

# Design System Output Validation Hook

This hook validates generated UI code against design system rules. It runs after Write or Edit tools modify `.tsx` or `.jsx` files.

## Validation Rules

### 1. No Hardcoded Colors

**Pattern to detect:**
```regex
(bg|text|border|ring)-(slate|gray|zinc|neutral|stone|red|orange|amber|yellow|lime|green|emerald|teal|cyan|sky|blue|indigo|violet|purple|fuchsia|pink|rose)-\d+
```

**Violations:**
- `bg-slate-900`
- `text-gray-500`
- `border-blue-200`

**Should be:**
- `bg-background` or `bg-primary`
- `text-foreground` or `text-muted-foreground`
- `border-border`

### 2. No Inline Color Styles

**Pattern to detect:**
```regex
style=\{.*color.*#[0-9a-fA-F]+
```

**Violations:**
- `style={{ color: '#1e293b' }}`
- `style={{ backgroundColor: '#3b82f6' }}`

### 3. Component Patterns

**Pattern to detect:**
```regex
className=\{`[^`]*\$\{[^}]+\}[^`]*`\}
```

**Violations:**
```typescript
className={`btn ${isPrimary ? 'bg-blue-500' : 'bg-gray-500'}`}
```

**Should use cva:**
```typescript
className={cn(buttonVariants({ variant }))}
```

### 4. Accessibility

**Pattern to detect:**
```regex
<div\s+onClick=
<span\s+onClick=
```

**Violations:**
```typescript
<div onClick={handleClick}>Click me</div>
```

**Should be:**
```typescript
<button type="button" onClick={handleClick}>Click me</button>
```

### 5. Icon Buttons Without Labels

**Pattern to detect:**
```regex
<Button[^>]*size="icon"[^>]*>(?!.*aria-label)
<button[^>]*>[\s]*<[A-Z][a-zA-Z]+[\s/]*>[\s]*</button>
```

**Violations:**
```typescript
<Button size="icon"><X /></Button>
<button><X /></button>
```

**Should have:**
```typescript
<Button size="icon" aria-label="Close"><X /></Button>
```

### 6. Any Types

**Pattern to detect:**
```regex
:\s*any\b
```

**Violations:**
```typescript
function Component(props: any) {}
const handleClick = (e: any) => {}
```

## Validation Response Format

When violations are found, output:

```markdown
## Design System Validation

### Violations Found

1. **Hardcoded color** at line X
   - Found: `bg-slate-900`
   - Should be: `bg-background` or `bg-primary`

2. **Accessibility issue** at line Y
   - Found: `<div onClick={...}>`
   - Should be: `<button type="button" onClick={...}>`

### Recommended Fixes

[Provide specific code changes]
```

## Auto-Fix Suggestions

For common violations, provide automatic fix suggestions:

| Violation | Auto-fix |
|-----------|----------|
| `bg-slate-900` | `bg-background` |
| `bg-slate-50` | `bg-background` |
| `text-slate-900` | `text-foreground` |
| `text-slate-500` | `text-muted-foreground` |
| `border-slate-200` | `border-border` |
| `bg-blue-500` | `bg-primary` |
| `text-blue-500` | `text-primary` |
| `bg-red-500` | `bg-destructive` |

## Skip Validation

To skip validation for specific lines, add a comment:

```typescript
// ds-ignore-next-line
className="bg-slate-900" // Legacy code, migration pending
```

Or for entire files:

```typescript
// ds-ignore-file
// This file uses custom theming for a specific feature
```

## Severity Levels

| Severity | Action |
|----------|--------|
| Error | Block and require fix (hardcoded colors, any types) |
| Warning | Allow but warn (missing aria-label) |
| Info | Suggest improvement (could use cva) |

## Integration

This hook integrates with:
- `/ds-review` skill for manual code review
- `component-builder` agent for automated generation
- CI/CD pipeline for PR checks

## Example Hook Implementation

```bash
#!/bin/bash
# hooks/validate-output.sh

FILE="$1"
CONTENT=$(cat "$FILE")

# Check for hardcoded colors
if echo "$CONTENT" | grep -E "(bg|text|border)-(slate|gray|blue|red)-[0-9]+" > /dev/null; then
  echo "ERROR: Hardcoded colors detected. Use semantic tokens instead."
  echo "Examples:"
  echo "  bg-slate-900 → bg-background"
  echo "  text-slate-500 → text-muted-foreground"
  echo "  border-blue-200 → border-border"
  exit 1
fi

# Check for any types
if echo "$CONTENT" | grep -E ":\s*any\b" > /dev/null; then
  echo "ERROR: 'any' type detected. Use proper TypeScript types."
  exit 1
fi

# Check for clickable divs
if echo "$CONTENT" | grep -E "<div\s+onClick=" > /dev/null; then
  echo "WARNING: Clickable div detected. Consider using <button> for accessibility."
fi

echo "Validation passed!"
exit 0
```
