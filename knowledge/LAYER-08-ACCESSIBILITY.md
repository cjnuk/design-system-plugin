# LAYER-08: ACCESSIBILITY (A11Y)

**File:** LAYER-08-ACCESSIBILITY.md  
**Purpose:** WCAG AA compliance standards and implementation  
**Audience:** All team members, QA, developers  
**Last Updated:** January 18, 2026

---

## TABLE OF CONTENTS

1. [Standards Overview](#standards-overview)
2. [Color Contrast](#color-contrast)
3. [Keyboard Navigation](#keyboard-navigation)
4. [ARIA Attributes](#aria-attributes)
5. [Semantic HTML](#semantic-html)
6. [Focus Management](#focus-management)
7. [Reduced Motion](#reduced-motion)
8. [Screen Readers](#screen-readers)
9. [Testing](#testing)
10. [Quick Checklist](#quick-checklist)
11. [Tools & Resources](#tools--resources)
12. [Summary](#summary)

---

## STANDARDS OVERVIEW

### WCAG 2.1 AA Compliance

We target **WCAG 2.1 Level AA** as our accessibility standard.

| Level | Criteria | Target |
|-------|----------|--------|
| **A** | Minimum accessibility | NOT our target |
| **AA** | Enhanced accessibility | ✅ **OUR TARGET** |
| **AAA** | Optimal accessibility | Optional (nice to have) |

### Key Requirements (AA Level)

1. **Perceivable** — Users can see and hear content
2. **Operable** — Users can navigate with keyboard
3. **Understandable** — Content is clear and language is simple
4. **Robust** — Works with assistive technologies

---

## COLOR CONTRAST

### Contrast Ratios Required (AA)

| Type | Ratio | Example |
|------|-------|---------|
| **Normal text** | 4.5:1 | Black text (#000) on white (#fff) |
| **Large text** (18px+) | 3:1 | Easier to meet |
| **UI Components** | 3:1 | Buttons, inputs, borders |
| **Graphical Elements** | 3:1 | Icons, diagrams |

### Checking Contrast

```jsx
// ✅ GOOD: White text on dark blue (14.7:1 ratio)
<button className="bg-blue-900 text-white">
  Click me
</button>

// ❌ BAD: Light gray text on light background (2.1:1 ratio)
<button className="bg-gray-100 text-gray-300">
  Click me
</button>

// ✅ GOOD: Dark text on light background (9.5:1 ratio)
<button className="bg-white text-gray-900 border border-gray-300">
  Click me
</button>
```

### Tools

- **WebAIM Contrast Checker:** https://webaim.org/resources/contrastchecker/
- **Chrome DevTools:** Lighthouse > Accessibility
- **Tailwind Config:** Pre-verified color system

---

## KEYBOARD NAVIGATION

### Tab Order (Most Important)

Users should navigate through interactive elements in logical order.

```jsx
// ✅ DO: Logical tab order
<form>
  <input placeholder="Name" /> {/* Tab: 1 */}
  <input placeholder="Email" /> {/* Tab: 2 */}
  <button>Submit</button> {/* Tab: 3 */}
</form>

// ❌ DON'T: Chaotic tab order with tabIndex
<form>
  <button tabIndex="3">Submit</button> {/* Tab: 1 ← Wrong! */}
  <input tabIndex="1" placeholder="Name" /> {/* Tab: 2 */}
  <input tabIndex="2" placeholder="Email" /> {/* Tab: 3 */}
</form>
```

### Skip Links

Provide a way to skip repetitive content.

```jsx
// ✅ DO: Add skip link
export function Layout() {
  return (
    <div>
      <a href="#main-content" className="sr-only focus:not-sr-only">
        Skip to main content
      </a>
      
      <header>Navigation and header content</header>
      
      <main id="main-content">
        Page content here
      </main>
    </div>
  );
}
```

### Keyboard Shortcuts

Implement essential keyboard shortcuts.

```jsx
// Essential shortcuts
- Tab: Move to next element
- Shift+Tab: Move to previous element
- Enter: Activate button/submit form
- Space: Toggle checkbox/button
- Escape: Close modal/dialog
- Arrow keys: Navigate lists/menus
```

---

## ARIA ATTRIBUTES

### aria-label (Accessible Name)

Use when visual text isn't available.

```jsx
// ✅ DO: Label icon buttons
import { X } from 'lucide-react';

export function CloseButton() {
  return (
    <button aria-label="Close dialog">
      <X size={20} />
    </button>
  );
}

// ❌ DON'T: Icon without label
<button>
  <X size={20} />
</button>
```

### aria-describedby (Accessible Description)

Provide additional information.

```jsx
// ✅ DO: Describe complex content
export function PasswordInput() {
  return (
    <div>
      <label htmlFor="password">Password</label>
      <input
        id="password"
        type="password"
        aria-describedby="password-hint"
      />
      <p id="password-hint" className="text-sm text-gray-600">
        Minimum 8 characters, including uppercase and numbers
      </p>
    </div>
  );
}
```

### aria-live (Live Regions)

Announce dynamic content changes.

```jsx
// ✅ DO: Announce form errors
export function Form() {
  const [errors, setErrors] = useState([]);

  return (
    <div>
      <div
        aria-live="polite"
        aria-atomic="true"
        className={errors.length > 0 ? 'block' : 'hidden'}
      >
        {errors.length > 0 && (
          <p className="text-red-600">
            {errors.length} error(s) in form. Please correct them.
          </p>
        )}
      </div>

      {/* Form content */}
    </div>
  );
}
```

### Common ARIA Attributes

| Attribute | Use Case |
|-----------|----------|
| `aria-label` | Accessible name for icon buttons |
| `aria-labelledby` | Link element to its label |
| `aria-describedby` | Add description to input |
| `aria-live` | Announce dynamic content |
| `aria-hidden` | Hide from assistive tech |
| `aria-disabled` | Mark non-HTML disabled |
| `aria-expanded` | Accordion/collapse state |
| `aria-selected` | Tab/menu selected state |
| `role` | Define element's role |

---

## SEMANTIC HTML

### Use Semantic Elements

```jsx
// ✅ DO: Semantic HTML
<header>Header content</header>
<nav>Navigation links</nav>
<main>Main content</main>
<article>Blog post</article>
<section>Content section</section>
<aside>Sidebar</aside>
<footer>Footer content</footer>

// ❌ DON'T: Div everywhere
<div>Header content</div>
<div>Navigation links</div>
<div>Main content</div>
```

### Heading Hierarchy

```jsx
// ✅ DO: Correct heading order
<h1>Page Title</h1>
<h2>Section 1</h2>
<h3>Subsection 1.1</h3>
<h2>Section 2</h2>

// ❌ DON'T: Skip levels
<h1>Page Title</h1>
<h3>Section (skip h2!)</h3>
<h5>Subsection (skip h3, h4!)</h5>
```

### Form Semantics

```jsx
// ✅ DO: Proper form structure
export function LoginForm() {
  return (
    <form>
      <div>
        <label htmlFor="email">Email</label>
        <input id="email" type="email" required />
      </div>
      <div>
        <label htmlFor="password">Password</label>
        <input id="password" type="password" required />
      </div>
      <button type="submit">Login</button>
    </form>
  );
}

// ❌ DON'T: Missing labels
<form>
  <input type="email" />
  <input type="password" />
  <button>Login</button>
</form>
```

---

## FOCUS MANAGEMENT

### Visible Focus Indicator

Ensure focus is always visible.

```jsx
// ✅ DO: Clear focus states
import { Button } from '@/components/ui/button';

export function FocusButton() {
  return (
    <Button className="focus-visible:outline-2 focus-visible:outline-blue-500">
      Click me
    </Button>
  );
}

// In CSS/Tailwind:
.focus-visible {
  outline: 2px solid #3b82f6;
  outline-offset: 2px;
}
```

### Focus on Modal Open

```jsx
// ✅ DO: Move focus to modal
import { useRef, useEffect } from 'react';

export function Modal({ isOpen, onClose }) {
  const firstButtonRef = useRef(null);

  useEffect(() => {
    if (isOpen) {
      firstButtonRef.current?.focus();
    }
  }, [isOpen]);

  return (
    <div className={`modal ${isOpen ? 'open' : 'closed'}`}>
      <h2>Modal Title</h2>
      <button ref={firstButtonRef}>First action</button>
      <button onClick={onClose}>Close</button>
    </div>
  );
}
```

### Focus Trap

Keep focus within modal.

```jsx
// ✅ DO: Focus trap in dialog
export function Dialog({ isOpen, onClose }) {
  const dialogRef = useRef(null);

  useEffect(() => {
    const handleKeyDown = (e) => {
      if (e.key === 'Tab') {
        const focusableElements = dialogRef.current?.querySelectorAll(
          'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
        );
        const firstElement = focusableElements?.[0];
        const lastElement = focusableElements?.[focusableElements.length - 1];

        if (e.shiftKey && document.activeElement === firstElement) {
          lastElement?.focus();
          e.preventDefault();
        } else if (!e.shiftKey && document.activeElement === lastElement) {
          firstElement?.focus();
          e.preventDefault();
        }
      }
    };

    if (isOpen) {
      dialogRef.current?.addEventListener('keydown', handleKeyDown);
    }

    return () => {
      dialogRef.current?.removeEventListener('keydown', handleKeyDown);
    };
  }, [isOpen]);

  return (
    <div ref={dialogRef} className={isOpen ? 'open' : 'hidden'}>
      {/* Dialog content */}
    </div>
  );
}
```

---

---

## REDUCED MOTION

Respect `prefers-reduced-motion: reduce` by minimizing motion and replacing complex animations with simple fades or instant state changes.

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

**Guidance:**
- Replace slide/scale transitions with opacity fades.
- Avoid parallax and continuous motion.
- Keep focus states visible and immediate.

---

## SCREEN READERS

### Alt Text for Images

```jsx
// ✅ DO: Meaningful alt text
<img src="user-avatar.jpg" alt="John Smith profile picture" />

// ✅ DO: Empty alt for decorative images
<img src="divider.png" alt="" aria-hidden="true" />

// ❌ DON'T: Useless alt text
<img src="user-avatar.jpg" alt="image" />
```

### Skip Repetitive Content

```jsx
// ✅ DO: Allow skipping navigation
export function Page() {
  return (
    <>
      <a href="#main" className="sr-only focus:not-sr-only">
        Skip to main content
      </a>
      <nav>{/* Navigation */}</nav>
      <main id="main">{/* Page content */}</main>
    </>
  );
}
```

### Announce Changes

```jsx
// ✅ DO: Use aria-live for updates
export function NotificationCount() {
  const [count, setCount] = useState(0);

  return (
    <div aria-live="polite" aria-atomic="true">
      You have {count} new message{count !== 1 ? 's' : ''}
    </div>
  );
}
```

---

## TESTING

### Automated Testing (Vitest + jest-axe)

```jsx
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';
import { test, expect } from 'vitest';

expect.extend(toHaveNoViolations);

test('button has no accessibility violations', async () => {
  const { container } = render(
    <button aria-label="Close">×</button>
  );
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

### Manual Testing Checklist

- [ ] **Keyboard Navigation**
  - [ ] Can tab through all interactive elements
  - [ ] Tab order is logical
  - [ ] Focus indicators are visible
  - [ ] Can close modals with Escape

- [ ] **Color & Contrast**
  - [ ] Text contrast is 4.5:1+ (normal text)
  - [ ] UI elements contrast is 3:1+
  - [ ] Not relying on color alone

- [ ] **Screen Reader**
  - [ ] Page title is meaningful
  - [ ] Headings are in order (h1 → h2 → h3)
  - [ ] Form labels are associated
  - [ ] Alt text on images is descriptive

- [ ] **Zoom & Magnification**
  - [ ] Content readable at 200% zoom
  - [ ] No horizontal scrolling at 200% zoom
  - [ ] Text scaling works

- [ ] **Semantics**
  - [ ] Using `<button>` for buttons (not divs)
  - [ ] Using `<a>` for links
  - [ ] Using `<label>` for form inputs
  - [ ] Heading hierarchy is correct

---

## QUICK CHECKLIST

### Every Component Needs

- [ ] **Keyboard accessible** — Can operate without mouse
- [ ] **Labeled** — Has aria-label or visual label
- [ ] **Focused** — Has visible focus indicator
- [ ] **Contrasting** — 4.5:1 ratio for text
- [ ] **Semantic** — Uses correct HTML elements
- [ ] **Reduced motion** — Respects `prefers-reduced-motion`

### Common Fixes

| Issue | Fix |
|-------|-----|
| Icon button unlabeled | Add `aria-label` |
| Low contrast text | Use darker color |
| Can't tab to element | Use `<button>` or add `role="button"` |
| Can't close with keyboard | Add Escape key handler |
| Form input not labeled | Add `<label>` with `htmlFor` |

---

## TOOLS & RESOURCES

### Testing Tools
- **Axe DevTools:** Browser extension for automated testing
- **WAVE:** WebAIM tool for visual feedback
- **Lighthouse:** Chrome DevTools accessibility audit
- **jest-axe:** Automated testing in unit tests

### Learning Resources
- **WCAG 2.1 Guidelines:** https://www.w3.org/WAI/WCAG21/quickref/
- **WebAIM Articles:** https://webaim.org/articles/
- **MDN Accessibility:** https://developer.mozilla.org/en-US/docs/Web/Accessibility
- **A11y Project:** https://www.a11yproject.com/

### Design System
All shadcn/ui components are accessibility-tested and meet AA standards by default.

---

## SUMMARY

✅ **Do:**
- Use semantic HTML (header, nav, main, footer, article, section)
- Ensure 4.5:1 contrast ratio for normal text
- Support keyboard navigation (Tab, Enter, Escape)
- Provide accessible names (labels, aria-label)
- Test with real assistive technology

❌ **Don't:**
- Use `<div>` for everything
- Rely on color alone to convey meaning
- Use autofocus on inputs
- Skip heading levels
- Disable zoom on mobile

---

**Next:** Implementation guides for marketing and application domains
