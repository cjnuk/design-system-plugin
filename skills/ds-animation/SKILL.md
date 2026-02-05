---
skill: ds-animation
description: Generate animation patterns following performance and accessibility guidelines
triggers:
  - "add animation"
  - "create animation"
  - "ds animation"
  - "animate component"
  - "motion pattern"
---

# Animation Pattern Generator

Generate animation code following performance and accessibility guidelines.

## Quick Reference

| Animation Type | Duration | Easing | Use Case |
|----------------|----------|--------|----------|
| fade | 150-200ms | ease-out | Show/hide elements |
| slide | 200-300ms | ease-out | Panel/drawer entry |
| scale | 150-200ms | ease-out | Modal, popover entry |
| bounce | 300-500ms | spring | Emphasis, success |

## Fade Animation

```css
/* Tailwind classes */
.fade-in {
  @apply animate-in fade-in duration-200;
}
.fade-out {
  @apply animate-out fade-out duration-150;
}
```

```tsx
// Component with fade
function FadeContent({ show, children }: { show: boolean; children: React.ReactNode }) {
  if (!show) return null

  return (
    <div className="animate-in fade-in duration-200">
      {children}
    </div>
  )
}
```

## Slide Animation

```css
/* Slide from bottom */
.slide-up {
  @apply animate-in slide-in-from-bottom-4 duration-300;
}

/* Slide from right (for drawers) */
.slide-left {
  @apply animate-in slide-in-from-right duration-300;
}
```

```tsx
// Drawer with slide animation
<Sheet>
  <SheetContent className="animate-in slide-in-from-right duration-300">
    {children}
  </SheetContent>
</Sheet>
```

## Scale Animation

```css
/* Scale up from center */
.scale-in {
  @apply animate-in zoom-in-95 duration-200;
}

/* Scale down to center */
.scale-out {
  @apply animate-out zoom-out-95 duration-150;
}
```

```tsx
// Modal with scale + fade
<DialogContent className="
  data-[state=open]:animate-in
  data-[state=open]:fade-in-0
  data-[state=open]:zoom-in-95
  data-[state=closed]:animate-out
  data-[state=closed]:fade-out-0
  data-[state=closed]:zoom-out-95
  duration-200
">
  {children}
</DialogContent>
```

## Stagger Animation

```tsx
// List items appear one by one
function StaggeredList({ items }: { items: string[] }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li
          key={item}
          className="animate-in fade-in slide-in-from-bottom-2"
          style={{ animationDelay: `${index * 50}ms`, animationFillMode: 'backwards' }}
        >
          {item}
        </li>
      ))}
    </ul>
  )
}
```

## Hover & Press States

```tsx
// Interactive button with micro-animations
<Button className="
  transition-all duration-150
  hover:scale-[1.02] hover:shadow-md
  active:scale-[0.98]
">
  Click me
</Button>
```

## Loading Spinner

```css
/* CSS spinner */
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 20px;
  height: 20px;
  border: 2px solid hsl(var(--muted));
  border-top-color: hsl(var(--primary));
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}
```

```tsx
// Spinner component
function Spinner({ size = 'md' }: { size?: 'sm' | 'md' | 'lg' }) {
  const sizes = { sm: 'h-4 w-4', md: 'h-6 w-6', lg: 'h-8 w-8' }

  return (
    <div className={`${sizes[size]} animate-spin rounded-full border-2 border-muted border-t-primary`} />
  )
}
```

## Reduced Motion Support

**Always include this in your global CSS:**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

**Conditional animation hook:**

```typescript
import { useEffect, useState } from 'react'

export function usePrefersReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false)

  useEffect(() => {
    const query = window.matchMedia('(prefers-reduced-motion: reduce)')
    setPrefersReducedMotion(query.matches)

    const handler = (e: MediaQueryListEvent) => setPrefersReducedMotion(e.matches)
    query.addEventListener('change', handler)
    return () => query.removeEventListener('change', handler)
  }, [])

  return prefersReducedMotion
}
```

## Tailwind Animation Config

Add to `tailwind.config.ts`:

```typescript
const config = {
  theme: {
    extend: {
      keyframes: {
        'fade-in': {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        'slide-up': {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
      animation: {
        'fade-in': 'fade-in 200ms ease-out',
        'slide-up': 'slide-up 300ms ease-out',
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
}

export default config
```

## Performance Checklist

- [ ] Uses only `transform` and `opacity` (GPU-accelerated)
- [ ] Duration < 300ms for interactions
- [ ] Includes `prefers-reduced-motion` fallback
- [ ] Uses CSS transitions for simple state changes
- [ ] Avoids animating layout properties (`width`, `height`, `margin`)

## Next Steps

- See [LAYER-07c-MOTION-PATTERNS](../../system/LAYER-07c-MOTION-PATTERNS.md) for comprehensive motion guide
- See [LAYER-08-ACCESSIBILITY](../../system/LAYER-08-ACCESSIBILITY.md) for reduced motion requirements
