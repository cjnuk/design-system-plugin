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

---

## COLOR SCALE USAGE GUIDE (Geist Pattern)

Each color scale has 10 steps (50-900) with semantic purposes:

| Steps | Purpose | Examples |
|-------|---------|----------|
| 50-200 | Backgrounds | Subtle backgrounds, hover states, disabled states |
| 300-400 | Borders | Dividers, input borders, separators |
| 600-900 | Text | Body text, headings, high-contrast elements |

#### Application Examples
- `blue-50` to `blue-200`: Use for tinted backgrounds, subtle highlights
- `blue-300` to `blue-400`: Use for borders, focus rings; `blue-500` is reserved for primary actions
- `blue-600` to `blue-900`: Use for text on light backgrounds

**LLM usage rule:** When selecting a color, first determine the purpose (background, border, or text), then select from the appropriate range.

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

## ELEVATION SYSTEM

Elevation creates visual hierarchy through shadows and z-index. This system distinguishes between in-page surfaces and floating overlays.

### Surface Elevation (In-page elements)

| Level | Shadow | Use Case |
|-------|--------|----------|
| base | none | Default page surface |
| raised | shadow-sm | Cards, panels |
| medium | shadow-md | Dropdown menus |
| high | shadow-lg | Modal dialogs |

### Floating Elevation (Overlay elements)

| Level | Shadow | Use Case |
|-------|--------|----------|
| tooltip | shadow-md + ring | Tooltips, popovers |
| menu | shadow-lg | Context menus, dropdowns |
| modal | shadow-xl | Dialogs, sheets |
| fullscreen | shadow-2xl | Full-screen overlays |

### Z-Index Mapping

| Elevation | z-index | Purpose |
|-----------|---------|---------|
| base | 0 | Default content |
| raised | 10 | Base interactive elements |
| floating | 30 | Dropdowns, tooltips, popovers |
| modal | 40 | Modals, dialogs |
| toast | 50 | Sticky headers, floating actions |

**LLM usage rule:** Always pair shadow tokens with appropriate z-index. A modal should use both `shadow-xl` AND `z-40`.

**See also:** [Z-INDEX TOKENS](#z-index-tokens) for the complete z-index specification and usage examples.

---

## WIDE GAMUT COLORS (P3)

Modern displays (particularly Apple devices) support Display P3 color space, which offers significantly more vibrant colors than traditional sRGB. This section documents how to leverage P3 while maintaining backward compatibility.

### What is Display P3?

Display P3 extends the sRGB color gamut, allowing approximately 50% more colors. This is especially noticeable for:
- Brand colors requiring maximum vibrancy
- Gradients with smoother color transitions
- Accent colors that need to stand out
- High-saturation UI elements

### CSS @supports Pattern

Always provide sRGB fallback first, then P3 in a `@supports` block:

```css
:root {
  /* sRGB baseline (universal support) */
  --primary-500: #3b82f6;
  --primary-600: #2563eb;
  --accent-500: #f59e0b;
  --success-500: #22c55e;
}

@supports (color: color(display-p3 1 1 1)) {
  :root {
    /* Display P3 enhanced colors */
    --primary-500: color(display-p3 0.231 0.510 0.965);
    --primary-600: color(display-p3 0.146 0.388 0.933);
    --accent-500: color(display-p3 0.961 0.619 0.043);
    --success-500: color(display-p3 0.133 0.773 0.361);
  }
}
```

### When to Use P3

| Use Case | Rationale | Impact |
|----------|-----------|--------|
| Brand colors | Maximum brand recognition on capable displays | High |
| Gradients | Smoother transitions, reduced banding | Medium |
| Accent colors | More visually striking UI elements | Medium |
| Secondary colors | Minor enhancement, not critical | Low |
| Grays/neutrals | Minimal visual difference | None |

### Implementation Strategy

#### Tailwind Integration

```typescript
// tailwind.config.ts
import colors from 'tailwindcss/colors'

export default {
  theme: {
    extend: {
      colors: {
        primary: {
          // sRGB (default fallback)
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          900: '#1e3a8a',

          // P3 variants (optional, for reference)
          // 500p3: 'color(display-p3 0.231 0.510 0.965)',
        },
      },
    },
  },
}
```

#### CSS Custom Properties (Preferred)

```css
/* Define in your CSS root */
:root {
  --primary: #3b82f6;
  --primary-dark: #2563eb;
}

@supports (color: color(display-p3 1 1 1)) {
  :root {
    --primary: color(display-p3 0.231 0.510 0.965);
    --primary-dark: color(display-p3 0.146 0.388 0.933);
  }
}

/* Use in components */
button {
  background-color: var(--primary);
}
```

### Gradient Example

```css
/* sRGB gradient (fallback) */
.gradient-hero {
  background: linear-gradient(
    135deg,
    #3b82f6 0%,
    #ec4899 100%
  );
}

/* Enhanced P3 gradient */
@supports (color: color(display-p3 1 1 1)) {
  .gradient-hero {
    background: linear-gradient(
      135deg,
      color(display-p3 0.231 0.510 0.965) 0%,
      color(display-p3 0.925 0.286 0.604) 100%
    );
  }
}
```

### Converting sRGB to P3

Use tools like:
- [Color Space Converter](https://colorspacious.readthedocs.io/)
- [Web Color Tools](https://www.colorhexa.com/)
- [Adobe Color](https://color.adobe.com/) (export as CSS)

**Conversion example:**
```
sRGB #3b82f6 (rgb 59, 130, 246)
→ Display P3 color(display-p3 0.231 0.510 0.965)
```

### Browser Support

| Browser | Display P3 Support |
|---------|-------------------|
| Chrome/Edge 77+ | Yes |
| Firefox 96+ | Yes |
| Safari 15+ | Yes |
| iOS Safari 15+ | Yes |
| Android Chrome | Yes |

Use `@supports` to safely implement P3 without worrying about older browsers; they'll use the sRGB fallback.

### Best Practices

1. **Always provide sRGB fallback:** Use `@supports` for safe P3 enhancement
2. **Test on real devices:** P3 rendering varies slightly between Apple and Android devices
3. **Don't overuse:** Reserve P3 for brand colors and high-impact accents
4. **Maintain consistency:** Ensure P3 variants are slightly more vibrant, not drastically different
5. **Document:** Add comments when using P3 so future maintainers understand the intent

### Verification

```typescript
// Test P3 support in JavaScript
const isP3Supported = CSS.supports('color', 'color(display-p3 1 1 1)')
console.log('Display P3 supported:', isP3Supported)

// Use in conditional styling
if (isP3Supported) {
  // Apply P3-optimized styles
}
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
