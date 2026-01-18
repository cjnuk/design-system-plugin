# LAYER 01: DESIGN TOKENS

**Purpose:** Complete specification of design tokens (colors, typography, spacing, shadows, motion, z-index, breakpoints). This is the single source of truth for all visual properties.

---

## OVERVIEW

Design tokens are the foundational variables that define your visual language. Every color, every spacing value, every shadow comes from here. Change a token, and it ripples everywhere.

```
Design Tokens (This Layer)
    ↓
Tailwind Config File
    ↓
Component Styling
    ↓
Marketing Site + Application
```

---

## COLOR TOKENS

### Primary Color Scale

Our primary color is a blue-based scale used for actions, links, and primary interactions.

```
blue-50:    #eff6ff  (lightest, backgrounds)
blue-100:   #dbeafe
blue-200:   #bfdbfe
blue-300:   #93c5fd
blue-400:   #60a5fa
blue-500:   #3b82f6  ← PRIMARY (buttons, links)
blue-600:   #2563eb  ← PRIMARY HOVER
blue-700:   #1d4ed8  ← PRIMARY ACTIVE
blue-800:   #1e40af
blue-900:   #1e3a8a  (darkest, text)
```

**Usage:**
- `blue-500`: Primary buttons, links, active states
- `blue-600`: Hover states (darker)
- `blue-700`: Pressed states (even darker)
- `blue-100`: Light backgrounds, subtle highlights

### Neutral Color Scale

Neutrals for text, borders, backgrounds, disabled states.

```
slate-50:   #f8fafc  (lightest background)
slate-100:  #f1f5f9
slate-200:  #e2e8f0
slate-300:  #cbd5e1  ← borders
slate-400:  #94a3b8
slate-500:  #64748b  ← secondary text
slate-600:  #475569  ← body text
slate-700:  #334155
slate-800:  #1e293b  ← headings
slate-900:  #0f172a  (darkest, primary text)
```

**Usage:**
- `slate-900`: Primary text, headings
- `slate-600`: Body text
- `slate-300`: Borders, dividers
- `slate-50`: Light backgrounds, disabled states

### Semantic Colors

Colors with specific meanings (success, error, warning, info).

#### Success Color
```
green-50:   #f0fdf4
green-500:  #22c55e  ← Success (borders, badges)
green-600:  #16a34a  ← Success hover
green-700:  #15803d  ← Success active
```
**Usage:** Success messages, checkmarks, confirmed states

#### Error Color
```
red-50:     #fef2f2
red-500:    #ef4444  ← Error (text, borders, backgrounds)
red-600:    #dc2626  ← Error hover
red-700:    #b91c1c  ← Error active
```
**Usage:** Error messages, validation errors, danger actions

#### Warning Color
```
amber-50:   #fffbeb
amber-500:  #f59e0b  ← Warning
amber-600:  #d97706  ← Warning hover
amber-700:  #b45309  ← Warning active
```
**Usage:** Warning messages, caution states, alerts

#### Info Color
```
cyan-50:    #ecf8fb
cyan-500:   #06b6d4  ← Info
cyan-600:   #0891b2  ← Info hover
cyan-700:   #0e7490  ← Info active
```
**Usage:** Informational messages, hints, learning content

### Semantic Surface Tokens (Light/Dark)

These tokens decouple UI surfaces from raw colors so themes can change without editing component classes.

```
surface-background:  slate-50
surface-1:           white
surface-2:           slate-100
surface-3:           slate-200
surface-text-primary:   slate-900
surface-text-secondary: slate-600
surface-text-muted:     slate-500
surface-border-subtle:  slate-200
surface-border-strong:  slate-300
```

**Dark Mode Mapping:**
```
surface-background:  slate-900
surface-1:           slate-800
surface-2:           slate-700
surface-3:           slate-600
surface-text-primary:   slate-50
surface-text-secondary: slate-300
surface-text-muted:     slate-400
surface-border-subtle:  slate-700
surface-border-strong:  slate-600
```

**LLM usage rule:** Prefer `surface-*` tokens over raw colors in UI output. Swap themes by changing the mapping above, not the component classes.

### Implementation in Tailwind

```typescript
// tailwind.config.ts
module.exports = {
  theme: {
    colors: {
      // Semantic
      primary: colors.blue,
      success: colors.green,
      error: colors.red,
      warning: colors.amber,
      info: colors.cyan,
      
      // Neutral (for text, borders, backgrounds)
      neutral: colors.slate,
      
      // Special
      white: '#ffffff',
      black: '#000000',
    }
  }
}
```

**In HTML/CSS:**
```html
<!-- Primary button -->
<button class="bg-primary-500 hover:bg-primary-600 text-white">
  Click me
</button>

<!-- Success badge -->
<span class="bg-green-50 text-green-700 border-green-200">
  Confirmed
</span>

<!-- Error message -->
<div class="bg-red-50 text-red-700 border-red-200">
  Something went wrong
</div>
```

---

## TYPOGRAPHY TOKENS

### Font Families

```
Font Stack (sans-serif):
  system-ui,
  -apple-system,
  BlinkMacSystemFont,
  'Segoe UI',
  Roboto,
  'Helvetica Neue',
  Arial,
  sans-serif

Font Stack (monospace):
  'Monaco',
  'Menlo',
  'Ubuntu Mono',
  'Consolas',
  'source-code-pro',
  monospace
```

### Font Sizes

```
xs:    12px  (12/16 = 0.75rem)
sm:    14px  (14/16 = 0.875rem)
base:  16px  (1rem) ← default
md:    18px  (1.125rem)
lg:    20px  (1.25rem)
xl:    24px  (1.5rem)
2xl:   30px  (1.875rem)
3xl:   36px  (2.25rem)
4xl:   48px  (3rem)
5xl:   60px  (3.75rem)
```

### Font Weights

```
thin:         100
extralight:   200
light:        300
normal:       400  ← default (body text)
medium:       500
semibold:     600  ← headings
bold:         700  ← strong emphasis
extrabold:    800
black:        900  ← not typically used
```

### Line Heights

```
tight:      1.25  (headings)
snug:       1.375
normal:     1.5   (body text) ← default
relaxed:    1.625
loose:      2.0   (spacing, readability)
```

### Letter Spacing

```
tighter:  -0.05em
tight:    -0.025em
normal:    0em     ← default
wide:      0.025em
wider:     0.05em
widest:    0.1em
```

### Usage Examples

```html
<!-- Heading (H1) -->
<h1 class="text-4xl font-bold leading-tight">
  Main Heading
</h1>

<!-- Heading (H2) -->
<h2 class="text-3xl font-semibold leading-snug">
  Section Heading
</h2>

<!-- Body text (default) -->
<p class="text-base font-normal leading-normal">
  This is normal body text with comfortable readability.
</p>

<!-- Small text (labels, captions) -->
<label class="text-sm font-medium">
  Form Label
</label>

<!-- Code/Monospace -->
<code class="font-mono text-sm bg-slate-100">
  const x = 42;
</code>
```

---

## SPACING TOKENS

We use a 4px base unit, creating a consistent scale.

```
0:      0px
1:      4px
2:      8px
3:      12px
4:      16px   ← most common
5:      20px
6:      24px
7:      28px
8:      32px   ← section spacing
9:      36px
10:     40px
12:     48px
16:     64px
20:     80px
24:     96px
32:     128px
```

### Padding Examples

```html
<!-- Comfortable padding inside a button -->
<button class="px-4 py-2">
  <!-- 16px left/right, 8px top/bottom -->
</button>

<!-- Padding for a card -->
<div class="p-6">
  <!-- 24px on all sides -->
</div>

<!-- Asymmetric padding (typical for content) -->
<div class="px-4 py-8">
  <!-- 16px left/right, 32px top/bottom -->
</div>
```

### Margin Examples

```html
<!-- Spacing between sections -->
<section class="mb-16">
  <!-- 64px margin below -->
</section>

<!-- Spacing between items in a list -->
<li class="mb-4">
  <!-- 16px margin below each item -->
</li>

<!-- Centering with margin -->
<div class="mx-auto">
  <!-- margin-left and margin-right auto -->
</div>
```

---

## SHADOW TOKENS

Shadows create depth and hierarchy. We use a subtle, professional shadow scale.

```
none:      no shadow
sm:        0 1px 2px 0 rgba(0, 0, 0, 0.05)
base:      0 1px 3px 0 rgba(0, 0, 0, 0.1),
           0 1px 2px 0 rgba(0, 0, 0, 0.06)
md:        0 4px 6px -1px rgba(0, 0, 0, 0.1),
           0 2px 4px -1px rgba(0, 0, 0, 0.06)
lg:        0 10px 15px -3px rgba(0, 0, 0, 0.1),
           0 4px 6px -2px rgba(0, 0, 0, 0.05)
xl:        0 20px 25px -5px rgba(0, 0, 0, 0.1),
           0 10px 10px -5px rgba(0, 0, 0, 0.04)
2xl:       0 25px 50px -12px rgba(0, 0, 0, 0.25)
inner:     inset 0 2px 4px 0 rgba(0, 0, 0, 0.05)
```

### Usage

```html
<!-- Card with subtle shadow (resting state) -->
<div class="bg-white shadow-md rounded-lg p-6">
  Content here
</div>

<!-- Button with no shadow (resting) -->
<button class="bg-blue-500 rounded px-4 py-2">
  Normal state
</button>

<!-- Button with shadow (hover/active) -->
<button class="bg-blue-500 hover:shadow-lg rounded px-4 py-2">
  Hover state (shadow appears)
</button>

<!-- Inset shadow (etchings, separations) -->
<input class="shadow-inner border rounded px-3 py-2" />
```

---

## BORDER RADIUS TOKENS

Controlled roundness for consistency.

```
none:      0px
sm:        2px   (subtle)
base:      4px   ← default (most elements)
md:        6px   (slightly more rounded)
lg:        8px   (cards, modals)
xl:        12px  (large containers)
2xl:       16px  (very rounded)
3xl:       24px
full:      9999px (completely rounded, pills)
```

### Usage

```html
<!-- Default rounding (buttons, inputs, small cards) -->
<button class="rounded px-4 py-2">
  <!-- 4px radius -->
</button>

<!-- Larger rounding (cards, containers) -->
<div class="rounded-lg p-6">
  <!-- 8px radius -->
</div>

<!-- Pill-shaped elements -->
<span class="rounded-full px-3 py-1 bg-blue-100">
  Badge
</span>

<!-- No rounding (rare, but sometimes needed) -->
<div class="rounded-none border">
  Sharp edges
</div>
```

---

## Z-INDEX TOKENS

Layering system for stacking contexts.

```
auto:    auto (default)
0:       0
10:      10    (base interactive elements)
20:      20    (overlays, modals)
30:      30    (dropdowns, tooltips, popovers)
40:      40    (modals, dialogs)
50:      50    (sticky headers, floating actions)
auto:    auto  (reset stacking context if needed)
```

### Usage

```html
<!-- Modal background -->
<div class="fixed inset-0 bg-black/50 z-40">
  <!-- z-40: above most content -->
  
  <!-- Modal dialog -->
  <div class="bg-white rounded-lg z-50">
    <!-- z-50: above the background -->
  </div>
</div>

<!-- Sticky header -->
<header class="sticky top-0 z-30 bg-white">
  <!-- z-30: above normal content but below modals -->
</header>

<!-- Tooltip -->
<div class="absolute z-30 bg-slate-900 text-white rounded">
  Tooltip
</div>
```

---

## MOTION/ANIMATION TOKENS

Timing and easing for smooth, professional animations.

### Durations

```
75ms:     ultra-fast (micro-interactions)
100ms:    very-fast (standard feedback)
150ms:    fast (most transitions)
200ms:    normal (default)
300ms:    slower (important state changes)
500ms:    slow (major page transitions)
700ms:    slower-still (entrance animations)
1000ms:   slowest (special effects)
```

### Easing Functions

```
linear:           constant speed
in:               slow start, fast finish
out:              fast start, slow finish
in-out:           slow start and finish
ease-in-cubic:    smooth acceleration
ease-out-cubic:   smooth deceleration
ease-in-out-quad: balanced smooth motion
```

### Usage

```css
/* Button hover (fast feedback) */
.button {
  transition: background-color 150ms ease-out;
}

/* Modal entrance (noticeable but quick) */
.modal {
  animation: fadeInScale 300ms ease-out;
}

/* Subtle color change (very fast) */
.text-hover {
  transition: color 75ms linear;
}
```

---

## BREAKPOINTS

Mobile-first responsive design.

```
xs:    320px   (phones, default)
sm:    640px   (landscape phones)
md:    768px   (tablets)
lg:    1024px  (desktops)
xl:    1280px  (large desktops)
2xl:   1536px  (very large screens)
```

### Usage

```html
<!-- Default (mobile) -->
<div class="text-sm">
  <!-- 12px text on mobile -->

  <!-- Larger on tablets -->
  md:text-base
  <!-- 16px text on tablets and up -->

  <!-- Even larger on desktops -->
  lg:text-lg
  <!-- 20px text on desktops and up -->
</div>

<!-- Mobile-first grid -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3">
  <!-- 1 column on mobile -->
  <!-- 2 columns on tablets -->
  <!-- 3 columns on desktops -->
</div>
```

---

## IMPLEMENTING TOKENS IN TAILWIND

Your `tailwind.config.ts` should look like:

```typescript
import type { Config } from 'tailwindcss'
import defaultTheme from 'tailwindcss/defaultTheme'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          // ... full scale ...
          900: '#1e3a8a',
        },
        success: {
          50: '#f0fdf4',
          500: '#22c55e',
          600: '#16a34a',
          700: '#15803d',
        },
        // ... other semantic colors ...
      },
      spacing: {
        0: '0px',
        1: '4px',
        2: '8px',
        // ... full scale ...
      },
      fontSize: {
        xs: ['12px', { lineHeight: '16px' }],
        sm: ['14px', { lineHeight: '20px' }],
        base: ['16px', { lineHeight: '24px' }],
        // ... full scale ...
      },
      // ... other token definitions ...
    },
  },
  plugins: [],
}

export default config
```

---

## TOKEN VERSIONING

When you update a token, document the change:

```markdown
## Token Changes - v2.0

### Added
- `blue-950`: Darkest blue for deep backgrounds
- `shadow-2xl`: Extra-large shadow for hero sections

### Changed
- `primary-500`: Changed from #3B82F6 to #2563EB (slightly darker for better contrast)

### Removed
- `cyan-100`: Merged into `blue-100` for simplification

### Why
- Improved color contrast (WCAG AA compliance)
- Added hero-specific shadows
- Simplified color palette (one blue scale instead of blue + cyan)
```

---

## NEXT STEPS

1. **Update your `tailwind.config.ts`** with these tokens
2. **Read [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md)** to see how components use these tokens
3. **Read [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md)** to understand CSS organization
