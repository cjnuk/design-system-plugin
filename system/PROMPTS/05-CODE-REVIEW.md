---
title: "Prompt: Code Review"
task_type: "review"
retrieval_path: ["LLM-AGENT-GUIDE", "00-MASTER", "LAYER-08-ACCESSIBILITY"]
---

# PROMPT: CODE REVIEW

Use this prompt when asking an LLM to review UI code against the design system.

---

## PROMPT TEMPLATE

```
Review the following code for design system compliance:

[PASTE CODE OR FILE PATH]

Check for:
1. Semantic token usage (vs hardcoded values)
2. Component composition patterns
3. Accessibility compliance
4. TypeScript type safety
5. Naming conventions
6. Performance considerations

Reference files:
- system/00-MASTER.md for system overview
- system/LAYER-01-DESIGN-TOKENS.md for token usage
- system/LAYER-08-ACCESSIBILITY.md for a11y requirements
```

---

## REVIEW CRITERIA

### 1. Token Usage

```typescript
// ❌ Hardcoded
className="bg-slate-900 text-white"

// ✅ Semantic tokens
className="bg-primary text-primary-foreground"
```

### 2. Component Composition

```typescript
// ❌ Prop drilling / monolithic
<CustomButton color="blue" size="large" rounded loading disabled>

// ✅ Composable with variants
<Button variant="default" size="lg" disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Loading
</Button>
```

### 3. Accessibility

```typescript
// ❌ Missing accessibility
<div onClick={handleClick}>Click me</div>

// ✅ Accessible
<button type="button" onClick={handleClick}>Click me</button>
```

### 4. Type Safety

```typescript
// ❌ Any types
function Component(props: any) { }

// ✅ Proper types
interface ComponentProps {
  title: string
  onAction: () => void
}
function Component({ title, onAction }: ComponentProps) { }
```

### 5. Naming

```typescript
// ❌ Inconsistent
const MyBtn = () => {}
const my_input = () => {}

// ✅ Consistent PascalCase for components
const Button = () => {}
const Input = () => {}
```

---

## OUTPUT FORMAT

```markdown
## Code Review Summary

### ✅ Compliant
- [List things done well]

### ⚠️ Suggestions
- [List minor improvements]

### ❌ Issues
- [List violations that must be fixed]

### Code Changes
[Provide specific code fixes for each issue]
```

---

## EXAMPLE OUTPUT

```markdown
## Code Review Summary

### ✅ Compliant
- Proper TypeScript types on props
- Using shadcn/ui Button component
- Logical component composition

### ⚠️ Suggestions
- Consider extracting repeated className patterns into cva variants
- Add data-testid for easier testing

### ❌ Issues
1. **Line 15**: Hardcoded color `bg-blue-500` should use `bg-primary`
2. **Line 23**: Icon button missing aria-label
3. **Line 31**: Using `any` type for event handler

### Code Changes

**Line 15:**
```diff
- className="bg-blue-500"
+ className="bg-primary"
```

**Line 23:**
```diff
- <Button size="icon"><X /></Button>
+ <Button size="icon" aria-label="Close"><X /></Button>
```
```
