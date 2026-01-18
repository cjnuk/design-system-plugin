---
skill: ds-tokens
description: Quick reference for design tokens - colors, spacing, typography, shadows, and more
triggers:
  - "design tokens"
  - "token reference"
  - "ds tokens"
  - "color tokens"
  - "spacing tokens"
  - "what token for"
---

# Design Tokens Quick Reference

Complete reference for all design system tokens. Use semantic tokens instead of hardcoded values.

## Color Tokens

### Semantic Surface Tokens

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `bg-background` | White | Slate 900 | Page background |
| `bg-foreground` | Slate 900 | Slate 50 | Inverted backgrounds |
| `bg-card` | White | Slate 800 | Card backgrounds |
| `bg-popover` | White | Slate 800 | Popover/dropdown backgrounds |
| `bg-muted` | Slate 100 | Slate 700 | Subtle backgrounds |
| `bg-accent` | Slate 100 | Slate 700 | Accent backgrounds |

### Text Tokens

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `text-foreground` | Slate 900 | Slate 50 | Primary text |
| `text-muted-foreground` | Slate 500 | Slate 400 | Secondary text |
| `text-card-foreground` | Slate 900 | Slate 50 | Card text |
| `text-popover-foreground` | Slate 900 | Slate 50 | Popover text |
| `text-accent-foreground` | Slate 900 | Slate 50 | Accent text |

### Interactive Tokens

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `bg-primary` | Blue 500 | Blue 400 | Primary buttons |
| `text-primary` | Blue 500 | Blue 400 | Primary links |
| `text-primary-foreground` | White | Slate 900 | Text on primary bg |
| `bg-secondary` | Slate 100 | Slate 700 | Secondary buttons |
| `text-secondary-foreground` | Slate 900 | Slate 50 | Text on secondary bg |
| `bg-destructive` | Red 500 | Red 600 | Danger buttons |
| `text-destructive` | Red 500 | Red 400 | Error text |

### Border & Ring Tokens

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `border-border` | Slate 200 | Slate 700 | Default borders |
| `border-input` | Slate 200 | Slate 700 | Input borders |
| `ring-ring` | Blue 500 | Blue 400 | Focus rings |

## CSS Custom Properties

```css
/* Use in CSS when needed */
hsl(var(--background))
hsl(var(--foreground))
hsl(var(--primary))
hsl(var(--primary-foreground))
hsl(var(--secondary))
hsl(var(--secondary-foreground))
hsl(var(--muted))
hsl(var(--muted-foreground))
hsl(var(--accent))
hsl(var(--accent-foreground))
hsl(var(--destructive))
hsl(var(--destructive-foreground))
hsl(var(--border))
hsl(var(--input))
hsl(var(--ring))
```

## Spacing Tokens

Based on 4px base unit:

| Token | Value | Usage |
|-------|-------|-------|
| `0` | 0px | Reset |
| `1` | 4px | Tight spacing |
| `2` | 8px | Icon gaps |
| `3` | 12px | Small gaps |
| `4` | 16px | Default padding |
| `5` | 20px | Medium padding |
| `6` | 24px | Card padding |
| `8` | 32px | Section spacing |
| `10` | 40px | Large gaps |
| `12` | 48px | Section margins |
| `16` | 64px | Page sections |
| `20` | 80px | Hero spacing |
| `24` | 96px | Large sections |

### Usage

```typescript
// Padding
className="p-4"     // 16px all sides
className="px-6"    // 24px left/right
className="py-2"    // 8px top/bottom

// Margin
className="mb-8"    // 32px bottom
className="mt-16"   // 64px top
className="mx-auto" // center horizontally

// Gap (flexbox/grid)
className="gap-4"   // 16px gap
className="gap-x-6" // 24px horizontal
className="gap-y-2" // 8px vertical
```

## Typography Tokens

### Font Sizes

| Token | Size | Line Height | Usage |
|-------|------|-------------|-------|
| `text-xs` | 12px | 16px | Labels, captions |
| `text-sm` | 14px | 20px | Small text, descriptions |
| `text-base` | 16px | 24px | Body text (default) |
| `text-lg` | 18px | 28px | Lead paragraphs |
| `text-xl` | 20px | 28px | H4 headings |
| `text-2xl` | 24px | 32px | H3 headings |
| `text-3xl` | 30px | 36px | H2 headings |
| `text-4xl` | 36px | 40px | H1 headings |
| `text-5xl` | 48px | 1 | Hero headings |

### Font Weights

| Token | Weight | Usage |
|-------|--------|-------|
| `font-normal` | 400 | Body text |
| `font-medium` | 500 | Labels, emphasis |
| `font-semibold` | 600 | Subheadings |
| `font-bold` | 700 | Headings |

### Usage

```typescript
// Heading
className="text-3xl font-semibold"

// Body
className="text-base font-normal"

// Label
className="text-sm font-medium"

// Caption
className="text-xs text-muted-foreground"
```

## Shadow Tokens

| Token | Usage |
|-------|-------|
| `shadow-sm` | Subtle elevation |
| `shadow` | Default cards |
| `shadow-md` | Elevated cards |
| `shadow-lg` | Dropdowns, popovers |
| `shadow-xl` | Modals |
| `shadow-2xl` | Hero elements |

## Border Radius Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `rounded-sm` | 2px | Subtle rounding |
| `rounded` | 4px | Default (buttons, inputs) |
| `rounded-md` | 6px | Cards |
| `rounded-lg` | 8px | Large cards |
| `rounded-xl` | 12px | Containers |
| `rounded-full` | 9999px | Pills, avatars |

### Usage

```typescript
// Button
className="rounded"

// Card
className="rounded-lg"

// Badge/pill
className="rounded-full"

// Avatar
className="rounded-full"
```

## Z-Index Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `z-0` | 0 | Base |
| `z-10` | 10 | Interactive elements |
| `z-20` | 20 | Sticky elements |
| `z-30` | 30 | Tooltips, popovers |
| `z-40` | 40 | Modal backdrop |
| `z-50` | 50 | Modal content |

## Breakpoints

| Token | Min-width | Usage |
|-------|-----------|-------|
| `sm:` | 640px | Landscape phones |
| `md:` | 768px | Tablets |
| `lg:` | 1024px | Desktops |
| `xl:` | 1280px | Large desktops |
| `2xl:` | 1536px | Very large screens |

### Usage

```typescript
// Mobile-first responsive
className="text-sm md:text-base lg:text-lg"
className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3"
className="px-4 md:px-6 lg:px-8"
```

## Animation Tokens

### Durations

| Token | Value | Usage |
|-------|-------|-------|
| `duration-75` | 75ms | Micro-interactions |
| `duration-100` | 100ms | Fast feedback |
| `duration-150` | 150ms | Most transitions |
| `duration-200` | 200ms | Default |
| `duration-300` | 300ms | State changes |
| `duration-500` | 500ms | Page transitions |

### Easing

```typescript
// Ease out (most common)
className="transition-all duration-150 ease-out"

// Ease in-out
className="transition-colors duration-200 ease-in-out"
```

## Quick Lookup Table

| Need | Token |
|------|-------|
| Page background | `bg-background` |
| Card background | `bg-card` |
| Primary button | `bg-primary text-primary-foreground` |
| Secondary button | `bg-secondary text-secondary-foreground` |
| Danger button | `bg-destructive text-destructive-foreground` |
| Body text | `text-foreground` |
| Secondary text | `text-muted-foreground` |
| Default border | `border-border` |
| Focus ring | `ring-ring` |
| Standard padding | `p-4` |
| Card padding | `p-6` |
| Section margin | `mb-8` or `mb-16` |
| Default rounded | `rounded` |
| Card rounded | `rounded-lg` |
| Default shadow | `shadow-md` |
