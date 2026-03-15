---
title: "Appendix C: Radix UI Primitives Reference"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["LAYER-02-COMPONENTS"]
last_updated: "2026-01-18"
---

# APPENDIX C: RADIX UI PRIMITIVES REFERENCE

**Purpose:** Understanding the Radix UI primitives that power shadcn/ui components.

---

## WHAT ARE HEADLESS PRIMITIVES?

Radix UI provides **unstyled, accessible UI primitives**. They handle:
- ✅ Accessibility (ARIA attributes, keyboard navigation)
- ✅ State management (open/closed, selected, focused)
- ✅ Event handling (click outside, escape key, focus trap)
- ❌ Visual styling (you add this with Tailwind)

**Key Insight:** shadcn/ui = Radix primitives + Tailwind styling

---

## COMPONENT COMPOSITION MODEL

Radix uses strict composition. Always follow the structure:

```jsx
// ✅ CORRECT: Follow composition pattern
<Dialog>
  <DialogTrigger>Open</DialogTrigger>
  <DialogPortal>
    <DialogOverlay />
    <DialogContent>
      <DialogTitle>Title</DialogTitle>
      <DialogDescription>Description</DialogDescription>
      {/* Your content */}
      <DialogClose>Close</DialogClose>
    </DialogContent>
  </DialogPortal>
</Dialog>

// ❌ WRONG: Skipping wrapper components
<DialogContent>Content</DialogContent>  // Won't work without Dialog parent
```

---

## KEY PRIMITIVES REFERENCE

### Dialog

**What it does:**
- Manages modal state (open/closed)
- Traps focus within modal
- Handles Escape key to close
- Prevents body scroll when open
- Sets `aria-modal="true"`

**Composition:**
```jsx
<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <DialogTrigger asChild>
    <Button>Open Dialog</Button>
  </DialogTrigger>
  <DialogPortal>
    <DialogOverlay className="fixed inset-0 bg-black/50" />
    <DialogContent className="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded-lg">
      <DialogTitle>Modal Title</DialogTitle>
      <DialogDescription>Modal description text.</DialogDescription>
      {/* Content */}
      <DialogClose asChild>
        <Button variant="ghost">Close</Button>
      </DialogClose>
    </DialogContent>
  </DialogPortal>
</Dialog>
```

**Accessibility guarantees:**
- Focus moves to first focusable element on open
- Focus returns to trigger on close
- Screen readers announce dialog title

---

### Popover

**What it does:**
- Positions content relative to trigger
- Handles click-outside to close
- Manages arrow positioning
- Supports collision detection (flip/shift)

**Composition:**
```jsx
<Popover>
  <PopoverTrigger asChild>
    <Button>Open Popover</Button>
  </PopoverTrigger>
  <PopoverPortal>
    <PopoverContent className="bg-white p-4 rounded-lg shadow-lg" sideOffset={5}>
      <PopoverArrow className="fill-white" />
      {/* Content */}
      <PopoverClose />
    </PopoverContent>
  </PopoverPortal>
</Popover>
```

**Position options:**
- `side`: "top" | "right" | "bottom" | "left"
- `align`: "start" | "center" | "end"
- `sideOffset`: number (gap from trigger)
- `alignOffset`: number (alignment adjustment)

---

### Dropdown Menu

**What it does:**
- Keyboard navigation (Arrow keys)
- Type-ahead search (type to jump to item)
- Nested submenus support
- Checkable/radio menu items

**Composition:**
```jsx
<DropdownMenu>
  <DropdownMenuTrigger asChild>
    <Button>Menu</Button>
  </DropdownMenuTrigger>
  <DropdownMenuPortal>
    <DropdownMenuContent className="bg-white p-1 rounded-lg shadow-lg min-w-[200px]">
      <DropdownMenuItem className="px-2 py-1.5 rounded hover:bg-slate-100 cursor-pointer">
        Item 1
      </DropdownMenuItem>
      <DropdownMenuItem>Item 2</DropdownMenuItem>
      <DropdownMenuSeparator className="h-px bg-slate-200 my-1" />
      <DropdownMenuSub>
        <DropdownMenuSubTrigger>More options →</DropdownMenuSubTrigger>
        <DropdownMenuSubContent>
          <DropdownMenuItem>Sub item</DropdownMenuItem>
        </DropdownMenuSubContent>
      </DropdownMenuSub>
    </DropdownMenuContent>
  </DropdownMenuPortal>
</DropdownMenu>
```

**Keyboard shortcuts:**
- `↓/↑`: Navigate items
- `→`: Open submenu
- `←`: Close submenu
- `Enter/Space`: Select item
- `Escape`: Close menu
- Type characters: Jump to matching item

---

### Tabs

**What it does:**
- Manages tab selection state
- Keyboard navigation between tabs
- ARIA attributes for tab list/panel
- Supports horizontal/vertical orientation

**Composition:**
```jsx
<Tabs defaultValue="tab1" className="w-full">
  <TabsList className="flex border-b">
    <TabsTrigger value="tab1" className="px-4 py-2 data-[state=active]:border-b-2 data-[state=active]:border-blue-500">
      Tab 1
    </TabsTrigger>
    <TabsTrigger value="tab2">Tab 2</TabsTrigger>
    <TabsTrigger value="tab3">Tab 3</TabsTrigger>
  </TabsList>
  <TabsContent value="tab1" className="p-4">
    Content for Tab 1
  </TabsContent>
  <TabsContent value="tab2" className="p-4">
    Content for Tab 2
  </TabsContent>
  <TabsContent value="tab3" className="p-4">
    Content for Tab 3
  </TabsContent>
</Tabs>
```

**Data attributes for styling:**
- `data-state="active"`: Currently selected tab
- `data-state="inactive"`: Unselected tabs
- `data-orientation="horizontal|vertical"`

---

### Accordion

**What it does:**
- Collapsible content sections
- Single or multiple items open
- Keyboard navigation
- Smooth expand/collapse animations

**Composition:**
```jsx
<Accordion type="single" collapsible className="w-full">
  <AccordionItem value="item-1">
    <AccordionTrigger className="flex justify-between w-full py-4 font-medium">
      Section 1
    </AccordionTrigger>
    <AccordionContent className="pb-4">
      Content for section 1
    </AccordionContent>
  </AccordionItem>
  <AccordionItem value="item-2">
    <AccordionTrigger>Section 2</AccordionTrigger>
    <AccordionContent>Content for section 2</AccordionContent>
  </AccordionItem>
</Accordion>
```

**Props:**
- `type="single"`: Only one item open at a time
- `type="multiple"`: Multiple items can be open
- `collapsible`: Allow closing all items (single type only)

---

### Select

**What it does:**
- Native-like dropdown with custom styling
- Keyboard navigation
- Typeahead search
- Grouped options support

**Composition:**
```jsx
<Select onValueChange={setValue}>
  <SelectTrigger className="w-full px-3 py-2 border rounded">
    <SelectValue placeholder="Select an option" />
  </SelectTrigger>
  <SelectPortal>
    <SelectContent className="bg-white rounded-lg shadow-lg">
      <SelectViewport>
        <SelectGroup>
          <SelectLabel className="px-2 py-1 text-sm text-slate-500">Fruits</SelectLabel>
          <SelectItem value="apple" className="px-2 py-1.5 hover:bg-slate-100 cursor-pointer">
            <SelectItemText>Apple</SelectItemText>
            <SelectItemIndicator>✓</SelectItemIndicator>
          </SelectItem>
          <SelectItem value="banana">Banana</SelectItem>
        </SelectGroup>
        <SelectSeparator />
        <SelectGroup>
          <SelectLabel>Vegetables</SelectLabel>
          <SelectItem value="carrot">Carrot</SelectItem>
        </SelectGroup>
      </SelectViewport>
    </SelectContent>
  </SelectPortal>
</Select>
```

---

### Tooltip

**What it does:**
- Shows on hover/focus
- Auto-hides on mouse leave
- Collision-aware positioning
- Delay before showing

**Composition:**
```jsx
<TooltipProvider delayDuration={200}>
  <Tooltip>
    <TooltipTrigger asChild>
      <Button>Hover me</Button>
    </TooltipTrigger>
    <TooltipPortal>
      <TooltipContent className="bg-slate-900 text-white px-2 py-1 rounded text-sm" sideOffset={5}>
        Tooltip text
        <TooltipArrow className="fill-slate-900" />
      </TooltipContent>
    </TooltipPortal>
  </Tooltip>
</TooltipProvider>
```

**Props:**
- `delayDuration`: ms before showing (default 700)
- `skipDelayDuration`: ms to skip delay after recent tooltip

---

### Checkbox

**What it does:**
- Controlled/uncontrolled boolean state
- Indeterminate state support
- Accessible labeling

**Composition:**
```jsx
<div className="flex items-center gap-2">
  <Checkbox 
    id="terms" 
    checked={checked}
    onCheckedChange={setChecked}
    className="w-4 h-4 border rounded data-[state=checked]:bg-blue-500"
  >
    <CheckboxIndicator>
      <CheckIcon />
    </CheckboxIndicator>
  </Checkbox>
  <label htmlFor="terms">Accept terms</label>
</div>
```

**States:**
- `data-state="checked"`: Checked
- `data-state="unchecked"`: Unchecked
- `data-state="indeterminate"`: Partial selection

---

### Switch

**What it does:**
- Toggle on/off state
- Accessible toggle button
- Animatable thumb

**Composition:**
```jsx
<div className="flex items-center gap-2">
  <Switch 
    id="dark-mode"
    checked={isDark}
    onCheckedChange={setIsDark}
    className="w-11 h-6 bg-slate-200 rounded-full data-[state=checked]:bg-blue-500"
  >
    <SwitchThumb className="block w-5 h-5 bg-white rounded-full transition-transform data-[state=checked]:translate-x-5" />
  </Switch>
  <label htmlFor="dark-mode">Dark mode</label>
</div>
```

---

## ACCESSIBILITY GUARANTEES

All Radix primitives provide:

| Feature | Automatic |
|---------|-----------|
| ARIA roles | ✅ |
| Keyboard navigation | ✅ |
| Focus management | ✅ |
| Screen reader support | ✅ |
| Semantic HTML | ✅ |
| Focus trapping (modals) | ✅ |
| Escape to close | ✅ |
| Click outside to close | ✅ |

**You only need to add:** Visual styling with Tailwind.

---

## COMMON DATA ATTRIBUTES

Use these for styling with Tailwind:

```css
/* Active/selected state */
data-[state=active]:bg-blue-500
data-[state=checked]:bg-green-500
data-[state=open]:rotate-180

/* Disabled state */
data-[disabled]:opacity-50
data-[disabled]:pointer-events-none

/* Orientation */
data-[orientation=horizontal]:flex-row
data-[orientation=vertical]:flex-col

/* Side (for positioned content) */
data-[side=top]:mb-2
data-[side=bottom]:mt-2
```

---

## LLM USAGE RULES

1. **Always follow composition** — Don't skip wrapper components
2. **Use `asChild`** — When you want to merge props onto your own element
3. **Add Tailwind classes** — Radix provides behavior, you provide styling
4. **Use data attributes** — For conditional styling based on state
5. **Wrap in Provider** — Some primitives need providers (Tooltip, Toast)

---

## RESOURCES

- **Radix UI Docs:** https://www.radix-ui.com/docs/primitives/overview/introduction
- **shadcn/ui Docs:** https://ui.shadcn.com/docs
- **Full API per component:** https://www.radix-ui.com/docs/primitives/components/dialog

---

**Related:** [LAYER-02-COMPONENTS](../LAYER-02-COMPONENTS.md) | [APPLICATION-DOMAIN-guide](../APPLICATION-DOMAIN-guide.md)
