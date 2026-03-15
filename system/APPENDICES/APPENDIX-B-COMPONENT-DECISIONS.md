---
title: "Appendix B: Component Decision Trees"
version: "2.1"
audience: ["developers", "designers", "llm-agents"]
dependencies: ["LAYER-02-COMPONENTS"]
last_updated: "2026-01-18"
---

# APPENDIX B: COMPONENT DECISION TREES

**Purpose:** Decision trees to help choose the right component for common UI patterns.

---

## BUTTON VS LINK

```
Does it navigate to a different page/URL?
├─ YES → Use <a> (or Link component in Next.js/React Router)
│        Classes: text-blue-500 hover:underline
└─ NO → Does it trigger an action (submit, toggle, open)?
    ├─ YES → Use <button>
    │        Classes: bg-blue-500 text-white px-4 py-2 rounded
    └─ NO → Does it look like a button but navigate?
        ├─ YES → Use <a> styled as button
        │        Classes: inline-block bg-blue-500 text-white px-4 py-2 rounded
        └─ NO → Use <button> with appropriate role
```

**LLM Rule:** When generating navigation, always use `<a>` or `Link`. When generating actions, always use `<button>`.

---

## DIALOG VS DRAWER VS SHEET

```
Is the content large (forms, settings, detailed info)?
├─ YES → On mobile?
│   ├─ YES → Use Sheet (bottom sheet, full-width)
│   └─ NO → Use Drawer (slides from side, 300-500px width)
└─ NO → Is it a confirmation/alert requiring user action?
    ├─ YES → Use AlertDialog (centered, blocks interaction)
    └─ NO → Use Dialog (centered modal, medium size)
```

**Size Guidelines:**
- **Dialog:** 400-600px width, centered
- **Drawer:** 300-500px width, slides from left/right
- **Sheet:** Full-width on mobile, 400px on desktop, slides from bottom
- **AlertDialog:** 350-450px width, centered, dimmed backdrop

---

## TOOLTIP VS POPOVER

```
Is the content short, read-only text?
├─ YES → Does it appear on hover/focus?
│   ├─ YES → Use Tooltip
│   │        - Max 1-2 lines
│   │        - Auto-dismiss on mouse leave
│   │        - No interactive content
│   └─ NO → Use Popover with close button
└─ NO → Is the content interactive (buttons, links, forms)?
    └─ YES → Use Popover
             - Triggered by click (not hover)
             - Contains interactive elements
             - Must be explicitly closed
```

**Accessibility:**
- Tooltip: `role="tooltip"`, linked via `aria-describedby`
- Popover: `role="dialog"` if interactive, focus trapped

---

## INPUT VS SELECT VS COMBOBOX

```
How many options are there?
├─ 2-4 options → Use Radio buttons (visible choices)
├─ 5-10 options → Use Select dropdown
├─ 10-50 options → Does user need to search/filter?
│   ├─ YES → Use Combobox (searchable select)
│   └─ NO → Use Select with scroll
├─ 50+ options → Use Combobox with async search
└─ Free text entry?
    └─ YES → Use Input (with optional datalist for suggestions)
```

**Form Pattern:**
```jsx
// Radio (2-4 options)
<RadioGroup>
  <RadioGroupItem value="a" />
  <RadioGroupItem value="b" />
</RadioGroup>

// Select (5-50 options)
<Select>
  <SelectTrigger />
  <SelectContent>
    <SelectItem value="..." />
  </SelectContent>
</Select>

// Combobox (searchable)
<Combobox>
  <ComboboxInput />
  <ComboboxOptions>
    <ComboboxOption value="..." />
  </ComboboxOptions>
</Combobox>
```

---

## TOAST VS ALERT VS ALERT DIALOG

```
Is this information critical/blocking?
├─ YES → Does user need to take action?
│   ├─ YES → Use AlertDialog (modal, requires response)
│   │        - "Delete account?" with Confirm/Cancel
│   │        - "Session expired" with Login button
│   └─ NO → Use Alert (inline, dismissible)
│            - Error message in form
│            - Warning banner at top of page
└─ NO → Is it temporary feedback?
    ├─ YES → Use Toast (auto-dismiss, non-blocking)
    │        - "Saved successfully"
    │        - "Message sent"
    │        - "Copied to clipboard"
    └─ NO → Use inline text or Badge
```

**Toast Duration Guidelines:**
- Success: 3 seconds
- Info: 4 seconds
- Warning: 5 seconds
- Error: 6 seconds (or persistent with close button)

---

## TABLE VS DATA TABLE VS CARD LIST

```
Is the data structured with multiple columns?
├─ YES → Does it need sorting/filtering/pagination?
│   ├─ YES → Use DataTable (with TanStack Table)
│   │        - Column sorting
│   │        - Global/column filters
│   │        - Row selection
│   │        - Pagination controls
│   └─ NO → Use basic Table component
│            - Simple header + rows
│            - Responsive (horizontal scroll on mobile)
└─ NO → Is it a list of items with rich content?
    ├─ YES → Use Card list (vertical stack of cards)
    │        - Each card: image + title + description + actions
    └─ NO → Use simple List component
             - ul/li with consistent styling
```

---

## TABS VS ACCORDION VS STEPPER

```
Are the sections mutually exclusive (show one at a time)?
├─ YES → Is the content related/parallel?
│   ├─ YES → Use Tabs (horizontal navigation)
│   │        - "Details | Reviews | Shipping"
│   │        - Content areas same size
│   └─ NO → Is it a multi-step process?
│       ├─ YES → Use Stepper (numbered steps)
│       │        - "1. Account → 2. Payment → 3. Confirm"
│       │        - Linear progression
│       └─ NO → Use Tabs with different content types
└─ NO → Can multiple sections be open?
    └─ YES → Use Accordion
             - FAQ sections
             - Collapsible settings
             - Single or multiple open allowed
```

---

## SKELETON VS SPINNER VS PROGRESS

```
What is being loaded?
├─ Content with known shape → Use Skeleton
│   - Card placeholders
│   - Text line placeholders
│   - Image placeholders
├─ Indeterminate operation → Use Spinner
│   - "Loading..." with no ETA
│   - Background refresh
│   - Quick operations (< 2 seconds expected)
└─ Operation with known progress → Use Progress bar
    - File upload (% complete)
    - Multi-step process
    - Download progress
```

**Timing Guidelines:**
- < 300ms: No indicator needed
- 300ms - 1s: Spinner
- 1s - 3s: Skeleton or spinner
- > 3s: Progress bar (if determinate) or skeleton + message

---

## BADGE VS TAG VS CHIP

```
Is it a status indicator?
├─ YES → Use Badge
│        - "New", "Sale", "Verified"
│        - Small, non-interactive
│        - Color indicates status (green=success, red=error)
├─ NO → Is it a category/label?
│   └─ YES → Use Tag
│            - "JavaScript", "Design", "Tutorial"
│            - Medium size, may be clickable (filter)
└─ NO → Is it a removable selection?
    └─ YES → Use Chip
             - Selected filter values
             - Has "x" button to remove
             - Interactive
```

---

## CHECKBOX VS SWITCH VS TOGGLE

```
Is it an immediate setting (takes effect now)?
├─ YES → Use Switch
│        - "Dark mode: ON/OFF"
│        - "Notifications: Enabled"
│        - Effect is instant
└─ NO → Is it part of a form (submitted later)?
    ├─ YES → Use Checkbox
    │        - "I agree to terms"
    │        - "Subscribe to newsletter"
    │        - Multiple selections in a group
    └─ NO → Is it a binary view toggle?
        └─ YES → Use Toggle buttons
                 - "Grid | List" view
                 - "Day | Week | Month"
                 - Segmented control appearance
```

---

## LLM DECISION QUICK REFERENCE

| Need | Component |
|------|-----------|
| Navigate to URL | `<a>` / `Link` |
| Trigger action | `<button>` |
| Modal with form | `Dialog` or `Drawer` |
| Confirmation | `AlertDialog` |
| Hover hint | `Tooltip` |
| Click popup with content | `Popover` |
| 2-4 choices | `RadioGroup` |
| 5+ dropdown choices | `Select` |
| Searchable choices | `Combobox` |
| Temporary message | `Toast` |
| Inline error/warning | `Alert` |
| Content loading | `Skeleton` |
| Action loading | `Spinner` |
| Upload/download | `Progress` |
| Status indicator | `Badge` |
| Category label | `Tag` |
| Removable selection | `Chip` |
| Instant toggle | `Switch` |
| Form boolean | `Checkbox` |

---

**Related:** [LAYER-02-COMPONENTS](../LAYER-02-COMPONENTS.md) | [LAYER-07-INTERACTION-PATTERNS](../LAYER-07-INTERACTION-PATTERNS.md)
