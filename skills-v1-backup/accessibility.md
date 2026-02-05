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
invoke_agent: accessibility-agent
---

# Fix Accessibility Issues

Dispatches to accessibility-agent for WCAG 2.1 AA compliance fixes.

The agent will:
1. Load PROMPTS/03-FIX-ACCESSIBILITY.md and LAYER-08-ACCESSIBILITY.md
2. Identify accessibility violations in the code
3. Apply fixes (aria-labels, keyboard handlers, semantic HTML)
4. Ensure color contrast ratios meet standards
5. Add screen reader support
6. Validate fixes with jest-axe patterns
