---
title: "Appendix A: Tailwind CSS Utility Reference"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["LAYER-01-DESIGN-TOKENS", "LAYER-04-CSS-ARCHITECTURE"]
last_updated: "2026-01-18"
---

# APPENDIX A: TAILWIND CSS UTILITY REFERENCE

**Purpose:** Quick reference for commonly used Tailwind CSS utilities organized by category.

---

## SPACING UTILITIES

### Margin
```
m-0 to m-96      → margin (all sides)
mx-*, my-*       → margin (horizontal/vertical)
mt-*, mb-*, ml-*, mr-* → margin (individual sides)
-m-* (negative)  → negative margins
```

### Padding
```
p-0 to p-96      → padding (all sides)
px-*, py-*       → padding (horizontal/vertical)
pt-*, pb-*, pl-*, pr-* → padding (individual sides)
```

### Gap (Flexbox/Grid)
```
gap-0 to gap-96  → gap between items
gap-x-*, gap-y-* → horizontal/vertical gap
```

### Common Values
```
0    → 0px
1    → 4px (0.25rem)
2    → 8px (0.5rem)
3    → 12px (0.75rem)
4    → 16px (1rem)
5    → 20px (1.25rem)
6    → 24px (1.5rem)
8    → 32px (2rem)
10   → 40px (2.5rem)
12   → 48px (3rem)
16   → 64px (4rem)
20   → 80px (5rem)
24   → 96px (6rem)
```

---

## TYPOGRAPHY UTILITIES

### Font Size
```
text-xs    → 12px
text-sm    → 14px
text-base  → 16px (default)
text-lg    → 18px
text-xl    → 20px
text-2xl   → 24px
text-3xl   → 30px
text-4xl   → 36px
text-5xl   → 48px
text-6xl   → 60px
```

### Font Weight
```
font-thin       → 100
font-extralight → 200
font-light      → 300
font-normal     → 400
font-medium     → 500
font-semibold   → 600
font-bold       → 700
font-extrabold  → 800
font-black      → 900
```

### Text Alignment
```
text-left, text-center, text-right, text-justify
```

### Text Transform
```
uppercase, lowercase, capitalize, normal-case
```

### Line Height
```
leading-none    → 1
leading-tight   → 1.25
leading-snug    → 1.375
leading-normal  → 1.5
leading-relaxed → 1.625
leading-loose   → 2
```

### Letter Spacing
```
tracking-tighter → -0.05em
tracking-tight   → -0.025em
tracking-normal  → 0
tracking-wide    → 0.025em
tracking-wider   → 0.05em
tracking-widest  → 0.1em
```

---

## COLOR UTILITIES

### Text Color
```
text-slate-900      → dark text
text-slate-600      → body text
text-slate-500      → muted text
text-blue-500       → primary/link
text-red-500        → error
text-green-500      → success
```

### Background Color
```
bg-white            → white background
bg-slate-50         → light gray background
bg-blue-500         → primary background
bg-red-50           → error background (light)
bg-green-50         → success background (light)
```

### Border Color
```
border-slate-200    → subtle border
border-slate-300    → standard border
border-red-500      → error border
border-blue-500     → focus border
```

### Ring (Focus Outline)
```
ring-2 ring-blue-500     → 2px blue focus ring
ring-offset-2            → offset from element
```

### Color Scales
```
Variants: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950
Palettes: slate, gray, zinc, neutral, stone, red, orange, amber, 
          yellow, lime, green, emerald, teal, cyan, sky, blue, 
          indigo, violet, purple, fuchsia, pink, rose
```

---

## LAYOUT UTILITIES

### Display
```
block, inline-block, inline, flex, grid, hidden
```

### Flexbox
```
flex-row, flex-col           → direction
flex-wrap, flex-nowrap       → wrapping
justify-start, justify-center, justify-end, justify-between, justify-around
items-start, items-center, items-end, items-stretch
flex-1, flex-auto, flex-none → grow/shrink
```

### Grid
```
grid-cols-1 to grid-cols-12  → column count
grid-rows-1 to grid-rows-6   → row count
col-span-1 to col-span-12    → column span
row-span-1 to row-span-6     → row span
```

### Width & Height
```
w-full, h-full              → 100%
w-screen, h-screen          → viewport
w-1/2, w-1/3, w-1/4         → fractions
w-auto, h-auto              → auto
max-w-sm to max-w-7xl       → max-width constraints
min-h-screen                → minimum height
```

### Position
```
static, relative, absolute, fixed, sticky
top-0, right-0, bottom-0, left-0
inset-0 (all sides)
z-10, z-20, z-30, z-40, z-50 → z-index
```

---

## RESPONSIVE PREFIXES

```
sm:   → 640px and up
md:   → 768px and up
lg:   → 1024px and up
xl:   → 1280px and up
2xl:  → 1536px and up
```

**Example:**
```html
<div class="text-sm md:text-base lg:text-lg">
  Responsive text
</div>
```

---

## STATE VARIANTS

### Hover/Focus/Active
```
hover:bg-blue-600         → on hover
focus:outline-2           → on focus
focus-visible:ring-2      → keyboard focus only
active:scale-95           → on click
disabled:opacity-50       → when disabled
```

### Group Hover
```html
<div class="group">
  <span class="group-hover:text-blue-500">
    Changes on parent hover
  </span>
</div>
```

### Dark Mode
```
dark:bg-slate-900         → dark mode background
dark:text-white           → dark mode text
```

---

## BORDER & SHADOW

### Border Width
```
border, border-0, border-2, border-4, border-8
border-t, border-r, border-b, border-l → individual sides
```

### Border Radius
```
rounded-none   → 0
rounded-sm     → 2px
rounded        → 4px
rounded-md     → 6px
rounded-lg     → 8px
rounded-xl     → 12px
rounded-2xl    → 16px
rounded-full   → 9999px (pill)
```

### Box Shadow
```
shadow-sm      → subtle
shadow         → default
shadow-md      → medium
shadow-lg      → large
shadow-xl      → extra large
shadow-2xl     → largest
shadow-none    → remove shadow
```

---

## TRANSITIONS & ANIMATIONS

### Transition
```
transition         → default (colors, opacity, shadow, transform)
transition-all     → all properties
transition-colors  → color properties only
transition-opacity → opacity only
transition-transform → transform only
duration-75, duration-100, duration-150, duration-200, duration-300, duration-500
ease-linear, ease-in, ease-out, ease-in-out
```

### Transform
```
scale-95, scale-100, scale-105    → scale
rotate-45, rotate-90, -rotate-45  → rotation
translate-x-1, translate-y-2      → translation
```

---

## ACCESSIBILITY UTILITIES

### Screen Reader Only
```
sr-only           → visually hidden, accessible to screen readers
not-sr-only       → undo sr-only
```

### Focus Visible
```
focus-visible:outline-2
focus-visible:ring-2
focus-visible:ring-blue-500
```

---

## LLM USAGE NOTE

When generating Tailwind classes:
1. Use semantic color tokens when available (e.g., `bg-primary-500` over `bg-blue-500`)
2. Prefer responsive mobile-first patterns (base → sm: → md: → lg:)
3. Always include focus states for interactive elements
4. Use `gap-*` over margin for spacing between flex/grid children

---

**Related:** [LAYER-01-DESIGN-TOKENS](../LAYER-01-DESIGN-TOKENS.md) | [LAYER-04-CSS-ARCHITECTURE](../LAYER-04-CSS-ARCHITECTURE.md)
