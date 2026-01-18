---
title: "Appendix D: Troubleshooting & FAQ"
version: "2.1"
audience: ["developers", "llm-agents", "qa"]
dependencies: []
last_updated: "2026-01-18"
---

# APPENDIX D: TROUBLESHOOTING & FAQ

**Purpose:** Solutions to common issues and frequently asked questions.

---

## STYLING ISSUES

### Q: Tailwind classes aren't applying

**Symptoms:** Classes are in the HTML but styles don't appear.

**Solutions:**

1. **Check content paths in `tailwind.config.ts`:**
```typescript
content: [
  './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
  './src/components/**/*.{js,ts,jsx,tsx,mdx}',
  './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  // Add any other directories with Tailwind classes
]
```

2. **Ensure Tailwind directives in global CSS:**
```css
/* globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

3. **Clear build cache:**
```bash
rm -rf .next
npm run build
```

4. **Check for typos in class names:**
```jsx
// ❌ Wrong
<div className="bg-blu-500">  // Typo: blu instead of blue

// ✅ Correct
<div className="bg-blue-500">
```

---

### Q: Styles are conflicting/overriding unexpectedly

**Solutions:**

1. **Use `!important` prefix sparingly:**
```jsx
<div className="!text-red-500">  // Forces override
```

2. **Check specificity order** — Later classes win:
```jsx
// ❌ bg-red won't show because bg-blue comes after
<div className="bg-red-500 bg-blue-500">

// ✅ Conditional classes
<div className={`${isError ? 'bg-red-500' : 'bg-blue-500'}`}>
```

3. **Use `clsx` or `cn` for conditional classes:**
```jsx
import { cn } from '@/lib/utils'

<div className={cn(
  'base-class',
  isActive && 'active-class',
  isError && 'error-class'
)}>
```

---

### Q: Dark mode isn't working

**Solutions:**

1. **Ensure `darkMode: 'class'` in config:**
```typescript
// tailwind.config.ts
export default {
  darkMode: 'class',
  // ...
}
```

2. **Add `dark` class to HTML element:**
```jsx
// Toggle dark mode
document.documentElement.classList.toggle('dark')
```

3. **Use `dark:` prefix correctly:**
```jsx
<div className="bg-white dark:bg-slate-900">
```

---

## COMPONENT ISSUES

### Q: How do I customize a shadcn/ui component?

**Three approaches:**

1. **Props** — Use built-in variants:
```jsx
<Button variant="outline" size="lg">
```

2. **className** — Add Tailwind overrides:
```jsx
<Button className="bg-purple-500 hover:bg-purple-600">
```

3. **Edit source** — Modify the component file directly:
```bash
# Component is in components/ui/button.tsx
# Edit it directly — you own the code
```

---

### Q: Component isn't accepting children

**Solution:** Check the component's TypeScript interface:

```tsx
// Ensure children prop is defined
interface MyComponentProps extends React.HTMLAttributes<HTMLDivElement> {
  children: React.ReactNode
}

export function MyComponent({ children, ...props }: MyComponentProps) {
  return <div {...props}>{children}</div>
}
```

---

### Q: shadcn/ui component not found

**Solutions:**

1. **Install the component:**
```bash
npx shadcn@latest add button
```

2. **Check import path:**
```jsx
// ✅ Correct (from your components/ui folder)
import { Button } from '@/components/ui/button'

// ❌ Wrong (don't import from shadcn directly)
import { Button } from 'shadcn/ui'
```

---

## RESPONSIVE DESIGN ISSUES

### Q: Layout breaks on mobile

**Debugging steps:**

1. **Use DevTools device mode** (Ctrl+Shift+M)
2. **Test at these widths:** 320px, 640px, 768px, 1024px
3. **Check for fixed widths** causing overflow:
```jsx
// ❌ Fixed width breaks on mobile
<div className="w-[600px]">

// ✅ Responsive width
<div className="w-full max-w-[600px]">
```

4. **Check images:**
```jsx
// ✅ Responsive images
<img className="w-full h-auto" />
```

---

### Q: How do I hide something on mobile?

```jsx
// Hidden on mobile, visible on md and up
<div className="hidden md:block">Desktop only</div>

// Visible on mobile, hidden on md and up
<div className="md:hidden">Mobile only</div>
```

---

## ACCESSIBILITY ISSUES

### Q: How do I test accessibility?

**Tools:**

1. **Lighthouse** (Chrome DevTools > Lighthouse > Accessibility)
2. **Axe DevTools** (browser extension)
3. **jest-axe** (unit tests):
```jsx
import { axe, toHaveNoViolations } from 'jest-axe'

expect.extend(toHaveNoViolations)

test('component is accessible', async () => {
  const { container } = render(<MyComponent />)
  const results = await axe(container)
  expect(results).toHaveNoViolations()
})
```
4. **Manual keyboard test:** Tab through all interactive elements

---

### Q: Focus indicator isn't visible

**Solution:** Add focus styles:
```jsx
<button className="focus-visible:outline-2 focus-visible:outline-blue-500 focus-visible:outline-offset-2">
```

---

### Q: Screen reader isn't reading my icon button

**Solution:** Add aria-label:
```jsx
<button aria-label="Close dialog">
  <XIcon />
</button>
```

---

## FORM ISSUES

### Q: Form validation isn't working

**Using React Hook Form:**
```jsx
import { useForm } from 'react-hook-form'

const { register, handleSubmit, formState: { errors } } = useForm()

<input 
  {...register('email', { 
    required: 'Email is required',
    pattern: {
      value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
      message: 'Invalid email'
    }
  })} 
/>
{errors.email && <span className="text-red-500">{errors.email.message}</span>}
```

---

### Q: Form submit refreshes the page

**Solution:** Prevent default:
```jsx
<form onSubmit={(e) => {
  e.preventDefault()
  handleSubmit()
}}>
```

Or with React Hook Form:
```jsx
<form onSubmit={handleSubmit(onSubmit)}>
```

---

## STATE MANAGEMENT ISSUES

### Q: Component re-renders too often

**Solutions:**

1. **Memoize with React.memo:**
```jsx
const MyComponent = React.memo(({ data }) => {
  return <div>{data}</div>
})
```

2. **Use useCallback for handlers:**
```jsx
const handleClick = useCallback(() => {
  // handler logic
}, [dependencies])
```

3. **Use useMemo for expensive calculations:**
```jsx
const processed = useMemo(() => {
  return expensiveCalculation(data)
}, [data])
```

4. **Select specific state in Jotai:**
```jsx
// ❌ Re-renders on any atom change if using entire store
const [state] = useAtom(storeAtom)

// ✅ Only re-renders when user changes
const user = useAtomValue(userAtom)
```

---

### Q: State updates aren't reflecting

**Solutions:**

1. **Don't mutate state directly:**
```jsx
// ❌ Wrong - mutating
state.items.push(newItem)
setState(state)

// ✅ Correct - new reference
setState({ ...state, items: [...state.items, newItem] })
```

2. **Check async timing:**
```jsx
// State update is async - won't log new value
setState(newValue)
console.log(state) // Still old value

// Use useEffect to react to changes
useEffect(() => {
  console.log(state) // New value
}, [state])
```

---

## PERFORMANCE ISSUES

### Q: Bundle size is too large

**Debugging:**
```bash
# Analyze bundle
npx @next/bundle-analyzer
```

**Solutions:**

1. **Dynamic imports:**
```jsx
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Skeleton />
})
```

2. **Tree shake imports:**
```jsx
// ❌ Imports entire library
import _ from 'lodash'

// ✅ Import specific function
import debounce from 'lodash/debounce'
```

3. **Check for duplicate dependencies**

---

### Q: Page loads slowly

**Checklist:**

- [ ] Images optimized (WebP, correct size)
- [ ] Fonts limited to 2-3 weights
- [ ] No unused CSS (Tailwind purges automatically)
- [ ] Dynamic imports for below-fold content
- [ ] Caching headers set
- [ ] Gzip/Brotli compression enabled

---

## BUILD ISSUES

### Q: Build fails with TypeScript errors

**Quick fixes:**

1. **Check for unused imports:**
```bash
# Many editors can auto-fix
```

2. **Add missing types:**
```typescript
// Install type definitions
npm install --save-dev @types/react @types/node
```

3. **Suppress specific errors (temporary):**
```typescript
// @ts-ignore (use sparingly)
```

---

### Q: Production build differs from development

**Causes:**

1. **CSS purging** — Production removes unused classes
   - Ensure dynamic classes are in safelist or full class names are used

2. **Environment variables** — Check `.env.production`

3. **Build cache** — Clear and rebuild:
```bash
rm -rf .next node_modules/.cache
npm run build
```

---

## COMMON ERROR MESSAGES

### "Hydration failed because the initial UI does not match"

**Cause:** Server and client render different content.

**Solutions:**

1. **Avoid browser-only code in initial render:**
```jsx
// ❌ Uses window on server
const [width, setWidth] = useState(window.innerWidth)

// ✅ Check for client
const [width, setWidth] = useState(0)
useEffect(() => {
  setWidth(window.innerWidth)
}, [])
```

2. **Use `suppressHydrationWarning` for intentional differences:**
```jsx
<time suppressHydrationWarning>{new Date().toLocaleString()}</time>
```

---

### "Cannot find module '@/components/...'"

**Solutions:**

1. **Check tsconfig.json paths:**
```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

2. **Restart TypeScript server** (VS Code: Ctrl+Shift+P > "Restart TS Server")

---

## LLM-SPECIFIC TROUBLESHOOTING

### When LLM-generated code doesn't work

1. **Check imports** — LLM may use wrong import paths
2. **Verify component exists** — May need to install via shadcn CLI
3. **Check prop names** — API may have changed
4. **Validate TypeScript** — Run `npx tsc --noEmit`

### When LLM generates outdated patterns

1. **Specify version** in your prompt: "Using shadcn/ui v2 and React 19"
2. **Provide context** from this design system
3. **Reference specific LAYER files** for accurate patterns

---

**Related:** [LAYER-04-CSS-ARCHITECTURE](../LAYER-04-CSS-ARCHITECTURE.md) | [APPLICATION-DOMAIN-guide](../APPLICATION-DOMAIN-guide.md)
