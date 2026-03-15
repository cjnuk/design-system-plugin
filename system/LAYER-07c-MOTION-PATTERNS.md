# LAYER-07c: MOTION PATTERNS

**File:** LAYER-07c-MOTION-PATTERNS.md
**Purpose:** Comprehensive motion design patterns for polished UI interactions
**Audience:** Frontend developers, designers, LLM agents
**Last Updated:** February 2026

---

## TABLE OF CONTENTS

1. [Philosophy](#philosophy)
2. [Micro-interactions](#micro-interactions)
3. [State Transitions](#state-transitions)
4. [Page Transitions](#page-transitions)
5. [Loading Sequences](#loading-sequences)
6. [Gesture Responses](#gesture-responses)
7. [Spring Animation](#spring-animation)
8. [Orchestration](#orchestration)
9. [Accessibility](#accessibility)

---

## PHILOSOPHY

Motion serves three purposes:
1. **Feedback** - Confirms user actions happened
2. **Guidance** - Directs attention to important changes
3. **Continuity** - Connects states to maintain spatial understanding

**Rules:**
- Motion serves function, not decoration
- Respect user preferences (`prefers-reduced-motion`)
- CSS-first, JavaScript-enhanced
- Faster is usually better (< 300ms for most interactions)

---

## MICRO-INTERACTIONS

### Hover States

```css
/* Button hover - subtle scale and color */
.button {
  transition: transform 150ms ease-out, background-color 150ms ease-out;
}
.button:hover {
  transform: scale(1.02);
}
.button:active {
  transform: scale(0.98);
}
```

### Focus States

```css
/* Ring appears with animation */
.input:focus-visible {
  outline: none;
  box-shadow: 0 0 0 2px hsl(var(--background)), 0 0 0 4px hsl(var(--ring));
  transition: box-shadow 150ms ease-out;
}
```

### Toggle Switches

```tsx
// Switch with spring-like motion
<Switch
  className="
    data-[state=checked]:bg-primary
    transition-colors duration-200
    [&>span]:transition-transform [&>span]:duration-200
    [&>span]:data-[state=checked]:translate-x-4
  "
/>
```

---

## STATE TRANSITIONS

### Expand/Collapse

```tsx
// Accordion with height animation
import { useState, useRef, useEffect } from 'react'

function Collapsible({ isOpen, children }: { isOpen: boolean; children: React.ReactNode }) {
  const contentRef = useRef<HTMLDivElement>(null)
  const [height, setHeight] = useState(0)

  useEffect(() => {
    if (contentRef.current) {
      setHeight(isOpen ? contentRef.current.scrollHeight : 0)
    }
  }, [isOpen])

  return (
    <div
      style={{ height, overflow: 'hidden' }}
      className="transition-[height] duration-300 ease-out"
    >
      <div ref={contentRef}>{children}</div>
    </div>
  )
}
```

### Show/Hide (Fade + Scale)

```tsx
// Modal entrance
<Dialog>
  <DialogContent className="
    data-[state=open]:animate-in
    data-[state=closed]:animate-out
    data-[state=closed]:fade-out-0
    data-[state=open]:fade-in-0
    data-[state=closed]:zoom-out-95
    data-[state=open]:zoom-in-95
    duration-200
  ">
    {children}
  </DialogContent>
</Dialog>
```

### Tab Switching

```css
/* Underline indicator slides to active tab */
.tab-indicator {
  position: absolute;
  bottom: 0;
  height: 2px;
  background: hsl(var(--primary));
  transition: left 200ms ease-out, width 200ms ease-out;
}
```

---

## PAGE TRANSITIONS

### Crossfade Between Routes

```tsx
// Using View Transitions API (modern browsers)
function navigateWithTransition(href: string) {
  if (!document.startViewTransition) {
    window.location.href = href
    return
  }

  document.startViewTransition(() => {
    window.location.href = href
  })
}

// CSS
::view-transition-old(root) {
  animation: fade-out 200ms ease-out;
}
::view-transition-new(root) {
  animation: fade-in 200ms ease-out;
}
```

### Slide Transitions

```tsx
// Page slides in from right
const pageVariants = {
  initial: { opacity: 0, x: 20 },
  animate: { opacity: 1, x: 0 },
  exit: { opacity: 0, x: -20 },
}

// CSS-only alternative
.page-enter {
  opacity: 0;
  transform: translateX(20px);
}
.page-enter-active {
  opacity: 1;
  transform: translateX(0);
  transition: opacity 200ms ease-out, transform 200ms ease-out;
}
```

---

## LOADING SEQUENCES

### Skeleton to Content

```tsx
function ContentWithSkeleton({ isLoading, children }: Props) {
  return (
    <div className="relative">
      {/* Skeleton fades out */}
      <div className={`
        absolute inset-0
        transition-opacity duration-300
        ${isLoading ? 'opacity-100' : 'opacity-0 pointer-events-none'}
      `}>
        <Skeleton className="h-full w-full" />
      </div>

      {/* Content fades in */}
      <div className={`
        transition-opacity duration-300
        ${isLoading ? 'opacity-0' : 'opacity-100'}
      `}>
        {children}
      </div>
    </div>
  )
}
```

### Staggered List Loading

```css
/* Items appear one by one */
.list-item {
  opacity: 0;
  transform: translateY(10px);
  animation: list-item-in 300ms ease-out forwards;
}

.list-item:nth-child(1) { animation-delay: 0ms; }
.list-item:nth-child(2) { animation-delay: 50ms; }
.list-item:nth-child(3) { animation-delay: 100ms; }
.list-item:nth-child(4) { animation-delay: 150ms; }
.list-item:nth-child(5) { animation-delay: 200ms; }

@keyframes list-item-in {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

---

## GESTURE RESPONSES

### Drag Feedback

```tsx
// Draggable card with tilt effect
function DraggableCard({ children }: { children: React.ReactNode }) {
  const [isDragging, setIsDragging] = useState(false)

  return (
    <div
      draggable
      onDragStart={() => setIsDragging(true)}
      onDragEnd={() => setIsDragging(false)}
      className={`
        transition-transform duration-150
        ${isDragging ? 'scale-105 rotate-2 opacity-80' : ''}
      `}
    >
      {children}
    </div>
  )
}
```

### Swipe Actions

```css
/* Swipe to reveal actions */
.swipe-item {
  transform: translateX(var(--swipe-x, 0));
  transition: transform 200ms ease-out;
}
.swipe-item.releasing {
  transition: transform 300ms cubic-bezier(0.32, 0.72, 0, 1);
}
```

---

## SPRING ANIMATION

### When to Use Springs

| Use Case | Timing Function |
|----------|-----------------|
| UI feedback | `ease-out` (CSS) |
| Dialogs, menus | `ease-out` (CSS) |
| Dragging/throwing | Spring (JS) |
| Physics-based | Spring (JS) |

### CSS Spring Approximation

```css
/* Bouncy spring effect with cubic-bezier */
.spring-bounce {
  transition: transform 500ms cubic-bezier(0.34, 1.56, 0.64, 1);
}

/* Smooth spring (less bounce) */
.spring-smooth {
  transition: transform 400ms cubic-bezier(0.32, 0.72, 0, 1);
}
```

### JavaScript Spring (for complex motion)

```typescript
// Simple spring physics
function spring(current: number, target: number, velocity: number, tension = 170, friction = 26) {
  const force = tension * (target - current)
  const damping = friction * velocity
  const acceleration = force - damping
  const newVelocity = velocity + acceleration * 0.001
  const newPosition = current + newVelocity * 0.001
  return { position: newPosition, velocity: newVelocity }
}
```

---

## ORCHESTRATION

### Staggered Animations

```tsx
// Children animate in sequence
function StaggeredList({ children }: { children: React.ReactNode[] }) {
  return (
    <div>
      {children.map((child, index) => (
        <div
          key={index}
          style={{ animationDelay: `${index * 50}ms` }}
          className="animate-in fade-in slide-in-from-bottom-2"
        >
          {child}
        </div>
      ))}
    </div>
  )
}
```

### Sequential Animations

```css
/* Parent animates, then children */
.card {
  animation: card-in 300ms ease-out forwards;
}
.card .card-title {
  opacity: 0;
  animation: fade-in 200ms ease-out 200ms forwards;
}
.card .card-content {
  opacity: 0;
  animation: fade-in 200ms ease-out 300ms forwards;
}

@keyframes card-in {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
@keyframes fade-in {
  to { opacity: 1; }
}
```

---

## ACCESSIBILITY

### Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Reduced Motion Hook

```typescript
function usePrefersReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false)

  useEffect(() => {
    const query = window.matchMedia('(prefers-reduced-motion: reduce)')
    setPrefersReducedMotion(query.matches)

    const handler = (event: MediaQueryListEvent) => {
      setPrefersReducedMotion(event.matches)
    }
    query.addEventListener('change', handler)
    return () => query.removeEventListener('change', handler)
  }, [])

  return prefersReducedMotion
}

// Usage
function AnimatedComponent() {
  const prefersReducedMotion = usePrefersReducedMotion()

  return (
    <div className={prefersReducedMotion ? '' : 'animate-bounce'}>
      Content
    </div>
  )
}
```

### Safe Motion Defaults

| Instead of | Use |
|------------|-----|
| Auto-playing animations | User-triggered animations |
| Parallax scrolling | Static positioning |
| Flashing/strobing | Steady state changes |
| Large-scale motion | Subtle opacity/color changes |

---

## SUMMARY

| Pattern | Duration | Easing | When |
|---------|----------|--------|------|
| Micro-interaction | 100-200ms | ease-out | Hover, focus, press |
| State transition | 200-300ms | ease-out | Expand, show/hide |
| Page transition | 200-400ms | ease-out | Route changes |
| Spring animation | 300-500ms | spring | Drag, physics |
| Loading sequence | 300ms | ease-out | Skeleton → content |

**LLM usage rule:** Default to CSS transitions with `ease-out`. Only use JavaScript animation for physics-based motion or complex orchestration. Always provide reduced motion fallback.

---

**Related:** [LAYER-07-INTERACTION-PATTERNS](./LAYER-07-INTERACTION-PATTERNS.md) | [LAYER-08-ACCESSIBILITY](./LAYER-08-ACCESSIBILITY.md)
