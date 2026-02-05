---
skill: ds-accessibility
description: Fix accessibility issues to meet WCAG 2.1 AA compliance standards
triggers:
  - "fix accessibility"
  - "a11y fix"
  - "ds accessibility"
  - "wcag compliance"
  - "make accessible"
arguments:
  - name: issue
    description: The accessibility issue to fix (e.g., color contrast, keyboard nav, screen reader)
    required: false
---

# Fix Accessibility Issues

Fix WCAG 2.1 AA accessibility violations in UI components. This skill provides patterns for common fixes.

## Quick Reference

| Issue | Solution |
|-------|----------|
| Missing label | Add `<label>` or `aria-label` |
| Icon-only button | Add `aria-label="Description"` |
| Color contrast | Use semantic tokens with 4.5:1 ratio |
| Keyboard trap | Add Escape key handler |
| Focus not visible | Use `ring-ring` focus styles |
| Missing alt text | Add descriptive `alt` attribute |

## Common Fixes

### 1. Clickable Div → Button

```typescript
// BEFORE (inaccessible)
<div onClick={handleClick} className="cursor-pointer">
  Click me
</div>

// AFTER (accessible)
<button type="button" onClick={handleClick}>
  Click me
</button>
```

### 2. Icon-Only Button

```typescript
// BEFORE (inaccessible)
<button onClick={handleClose}>
  <X className="h-4 w-4" />
</button>

// AFTER (accessible)
<button
  type="button"
  onClick={handleClose}
  aria-label="Close dialog"
>
  <X className="h-4 w-4" />
</button>
```

### 3. Form Input Without Label

```typescript
// BEFORE (inaccessible)
<input type="email" placeholder="Email" />

// AFTER (accessible)
<div>
  <label htmlFor="email" className="sr-only">
    Email address
  </label>
  <input
    id="email"
    type="email"
    placeholder="Email"
    aria-describedby="email-error"
  />
  {error && (
    <span id="email-error" role="alert" className="text-destructive text-sm">
      {error}
    </span>
  )}
</div>
```

### 4. Color Contrast Issues

```typescript
// BEFORE (fails contrast - 2.1:1)
<span className="text-slate-400 bg-slate-100">
  Low contrast text
</span>

// AFTER (passes contrast - 4.5:1+)
<span className="text-muted-foreground bg-muted">
  Accessible contrast text
</span>
```

**Contrast Requirements:**
- Normal text: 4.5:1 minimum
- Large text (18px+ or 14px+ bold): 3:1 minimum
- UI components: 3:1 minimum

### 5. Focus Indicators

```typescript
// BEFORE (no visible focus)
<button className="bg-primary">Submit</button>

// AFTER (visible focus ring)
<button className="bg-primary focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2">
  Submit
</button>
```

### 6. Keyboard Navigation

```typescript
// Dialog with keyboard handling
function Dialog({ isOpen, onClose, children }) {
  React.useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === "Escape") onClose()
    }

    if (isOpen) {
      document.addEventListener("keydown", handleKeyDown)
      return () => document.removeEventListener("keydown", handleKeyDown)
    }
  }, [isOpen, onClose])

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="dialog-title"
    >
      <h2 id="dialog-title">Dialog Title</h2>
      {children}
    </div>
  )
}
```

### 7. Image Alt Text

```typescript
// BEFORE (no alt text)
<img src="/hero.jpg" />

// AFTER (descriptive alt text)
<img
  src="/hero.jpg"
  alt="Team collaborating around a whiteboard with sticky notes"
/>

// Decorative image (intentionally empty alt)
<img src="/decorative-pattern.svg" alt="" role="presentation" />
```

### 8. Reduced Motion

```typescript
// Respect user preference for reduced motion
const prefersReducedMotion = window.matchMedia(
  "(prefers-reduced-motion: reduce)"
).matches

// In Tailwind
<div className="transition-transform motion-reduce:transition-none">
  Animated content
</div>

// Or with CSS
@media (prefers-reduced-motion: reduce) {
  .animated {
    animation: none;
    transition: none;
  }
}
```

### 9. Skip Link

```typescript
// Add skip link for keyboard users
<a
  href="#main-content"
  className="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-50 focus:px-4 focus:py-2 focus:bg-background focus:text-foreground focus:rounded"
>
  Skip to main content
</a>

<main id="main-content" tabIndex={-1}>
  {/* Page content */}
</main>
```

### 10. Accessible Dropdown Menu

```typescript
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button variant="ghost" aria-label="Open menu">
      <MoreHorizontal className="h-4 w-4" />
    </Button>
  </DropdownMenuTrigger>
  <DropdownMenuContent align="end">
    <DropdownMenuItem>Edit</DropdownMenuItem>
    <DropdownMenuItem>Duplicate</DropdownMenuItem>
    <DropdownMenuItem className="text-destructive">
      Delete
    </DropdownMenuItem>
  </DropdownMenuContent>
</DropdownMenu>
```

## Testing Checklist

### Manual Testing

- [ ] **Keyboard navigation**: Tab through all interactive elements
- [ ] **Focus order**: Focus moves in a logical sequence
- [ ] **Focus visible**: Focus indicator is clearly visible
- [ ] **Escape closes**: Modals/dialogs close with Escape
- [ ] **Screen reader**: Test with VoiceOver (Mac) or NVDA (Windows)

### Automated Testing

```typescript
// jest-axe for automated a11y testing
import { axe, toHaveNoViolations } from 'jest-axe'

expect.extend(toHaveNoViolations)

test('Button is accessible', async () => {
  const { container } = render(<Button>Click me</Button>)
  const results = await axe(container)
  expect(results).toHaveNoViolations()
})
```

### Tools

- [axe DevTools](https://www.deque.com/axe/devtools/) - Browser extension
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [WAVE](https://wave.webaim.org/) - Web accessibility evaluation
- jest-axe - Automated testing in Jest

## ARIA Roles Reference

| Role | Usage |
|------|-------|
| `button` | Clickable element that triggers action |
| `dialog` | Modal content |
| `alert` | Time-sensitive notification |
| `alertdialog` | Modal requiring user response |
| `tab` / `tablist` / `tabpanel` | Tab navigation |
| `menu` / `menuitem` | Navigation menu |
| `navigation` | Navigation landmark |
| `main` | Main content area |
| `complementary` | Supporting content (aside) |

## Screen Reader-Only Text

```typescript
// Tailwind utility for visually hidden text
<span className="sr-only">
  Additional context for screen readers
</span>

// Custom CSS equivalent
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

## Validation

After fixes, verify:

- [ ] All interactive elements keyboard accessible
- [ ] No focus traps
- [ ] Color contrast meets 4.5:1 (text) or 3:1 (UI)
- [ ] Images have appropriate alt text
- [ ] Form fields have labels
- [ ] Error messages announced to screen readers
- [ ] Reduced motion respected
- [ ] jest-axe tests pass
