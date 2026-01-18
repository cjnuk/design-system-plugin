---
title: "Prompt: Fix Accessibility Issue"
task_type: "accessibility-fix"
retrieval_path: ["LLM-AGENT-GUIDE", "LAYER-08-ACCESSIBILITY", "APPENDICES/APPENDIX-D-TROUBLESHOOTING"]
---

# PROMPT: FIX ACCESSIBILITY ISSUE

Use this prompt when asking an LLM to fix an accessibility violation.

---

## PROMPT TEMPLATE

```
Fix the following accessibility issue in [COMPONENT/FILE]:

Issue: [DESCRIBE THE VIOLATION]
WCAG criterion: [e.g., 1.4.3 Contrast, 2.1.1 Keyboard, 4.1.2 Name Role Value]
Current behavior: [What happens now]
Expected behavior: [What should happen]

Technical constraints:
- Maintain visual design intent
- Use semantic HTML where possible
- Prefer ARIA only when native semantics insufficient
- Ensure fix works with screen readers (VoiceOver, NVDA)

Reference files:
- system/LAYER-08-ACCESSIBILITY.md for a11y patterns
- system/APPENDICES/APPENDIX-D-TROUBLESHOOTING.md for common fixes
```

---

## EXAMPLE USAGE

```
Fix the following accessibility issue in components/ui/icon-button.tsx:

Issue: Icon button has no accessible name
WCAG criterion: 4.1.2 Name, Role, Value
Current behavior: Screen reader announces "button" with no label
Expected behavior: Screen reader announces purpose, e.g., "Close dialog, button"

Technical constraints:
- Maintain visual design intent
- Use semantic HTML where possible
- Prefer ARIA only when native semantics insufficient
- Ensure fix works with screen readers (VoiceOver, NVDA)
```

---

## COMMON FIXES BY ISSUE TYPE

### Missing Accessible Name

```typescript
// Before
<Button size="icon"><X /></Button>

// After - Option 1: aria-label
<Button size="icon" aria-label="Close dialog"><X /></Button>

// After - Option 2: visually-hidden text
<Button size="icon">
  <X aria-hidden="true" />
  <span className="sr-only">Close dialog</span>
</Button>
```

### Missing Focus Indicator

```typescript
// Add to component
className="focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
```

### Color Contrast

```typescript
// Use semantic tokens that meet contrast ratios
className="text-foreground"        // 7:1 on background
className="text-muted-foreground"  // 4.5:1 minimum
```

### Keyboard Navigation

```typescript
// Ensure interactive elements are focusable
<div
  role="button"
  tabIndex={0}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault()
      handleClick()
    }
  }}
>
```

---

## TESTING CHECKLIST

- [ ] Test with keyboard only (Tab, Enter, Space, Escape, Arrow keys)
- [ ] Test with VoiceOver (macOS) or NVDA (Windows)
- [ ] Run axe DevTools or jest-axe
- [ ] Verify color contrast with WebAIM contrast checker
- [ ] Check focus order is logical
