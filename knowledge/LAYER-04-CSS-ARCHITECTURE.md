# LAYER 04: CSS ARCHITECTURE

**Purpose:** How styling is organized, Tailwind configuration, file structure, naming conventions, and optimization strategies.

---

## TAILWIND CSS CONFIGURATION

Your `tailwind.config.ts` is the single source of truth for all styling decisions:

```typescript
import type { Config } from 'tailwindcss'
import defaultTheme from 'tailwindcss/defaultTheme'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        // Primary palette
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          200: '#bfdbfe',
          300: '#93c5fd',
          400: '#60a5fa',
          500: '#3b82f6',
          600: '#2563eb',
          700: '#1d4ed8',
          800: '#1e40af',
          900: '#1e3a8a',
        },
        // Semantic colors
        success: {
          50: '#f0fdf4',
          500: '#22c55e',
          600: '#16a34a',
          700: '#15803d',
        },
        error: {
          50: '#fef2f2',
          500: '#ef4444',
          600: '#dc2626',
          700: '#b91c1c',
        },
        warning: {
          50: '#fffbeb',
          500: '#f59e0b',
          600: '#d97706',
          700: '#b45309',
        },
        info: {
          50: '#ecf8fb',
          500: '#06b6d4',
          600: '#0891b2',
          700: '#0e7490',
        },
      },
      fontSize: {
        xs: ['12px', { lineHeight: '16px' }],
        sm: ['14px', { lineHeight: '20px' }],
        base: ['16px', { lineHeight: '24px' }],
        md: ['18px', { lineHeight: '28px' }],
        lg: ['20px', { lineHeight: '28px' }],
        xl: ['24px', { lineHeight: '32px' }],
        '2xl': ['30px', { lineHeight: '36px' }],
        '3xl': ['36px', { lineHeight: '44px' }],
        '4xl': ['48px', { lineHeight: '52px' }],
      },
      spacing: {
        0: '0px',
        1: '4px',
        2: '8px',
        3: '12px',
        4: '16px',
        5: '20px',
        6: '24px',
        8: '32px',
        10: '40px',
        12: '48px',
        16: '64px',
      },
      borderRadius: {
        none: '0px',
        sm: '2px',
        base: '4px',
        md: '6px',
        lg: '8px',
        xl: '12px',
        '2xl': '16px',
        full: '9999px',
      },
      boxShadow: {
        none: 'none',
        sm: '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
        base: '0 1px 3px 0 rgba(0, 0, 0, 0.1)',
        md: '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
        lg: '0 10px 15px -3px rgba(0, 0, 0, 0.1)',
        xl: '0 20px 25px -5px rgba(0, 0, 0, 0.1)',
      },
      transitionDuration: {
        75: '75ms',
        100: '100ms',
        150: '150ms',
        200: '200ms',
        300: '300ms',
      },
      keyframes: {
        fadeIn: {
          '0%': { opacity: '0' },
          '100%': { opacity: '1' },
        },
        slideIn: {
          '0%': { transform: 'translateY(10px)', opacity: '0' },
          '100%': { transform: 'translateY(0)', opacity: '1' },
        },
      },
      animation: {
        fadeIn: 'fadeIn 200ms ease-out',
        slideIn: 'slideIn 300ms ease-out',
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}

export default config
```

---

## FILE ORGANIZATION

```
src/
├── components/
│   ├── ui/                          # shadcn/ui generated components
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   ├── dialog.tsx
│   │   └── ... (50+ components)
│   ├── custom/                      # Your custom components
│   │   ├── ChatWindow.tsx
│   │   ├── MessageBubble.tsx
│   │   ├── SourcesPanel.tsx
│   │   └── CodeBlock.tsx
│   ├── layout/                      # Layout components
│   │   ├── Header.tsx
│   │   ├── Sidebar.tsx
│   │   ├── Footer.tsx
│   │   └── MainLayout.tsx
│   ├── marketing/                   # Marketing site components (Tailwind UI)
│   │   ├── Hero.tsx
│   │   ├── Features.tsx
│   │   ├── Pricing.tsx
│   │   └── CTA.tsx
│   └── forms/                       # Form-specific components
│       ├── LoginForm.tsx
│       ├── SignupForm.tsx
│       └── SettingsForm.tsx
├── styles/
│   ├── globals.css                  # Global styles, Tailwind imports
│   ├── animations.css               # Custom animations
│   └── utilities.css                # Custom utility classes (minimal)
├── lib/
│   ├── colors.ts                    # Color utilities
│   ├── spacing.ts                   # Spacing calculations
│   └── classNames.ts                # Class name helpers
├── pages/                           # or app/ (Next.js 13+)
│   ├── index.tsx
│   ├── dashboard.tsx
│   └── ...
```

---

## NAMING CONVENTIONS

### Component File Names
- **PascalCase** for components: `Button.tsx`, `ChatWindow.tsx`
- **camelCase** for utilities: `classNames.ts`, `formatDate.ts`
- **kebab-case** for CSS files: `globals.css`, `animations.css`

### Tailwind Class Names
Use descriptive, semantic names:

```tsx
// Good: clear intent
<div class="flex items-center justify-center gap-4">

// Bad: unclear
<div class="fc ai jc g4">
```

### CSS-in-JS (When Needed)

For truly dynamic styles, use `clsx` or `classnames`:

```tsx
import { clsx } from 'clsx'

interface ButtonProps {
  variant?: 'primary' | 'secondary'
  isLoading?: boolean
}

export function Button({ variant = 'primary', isLoading }: ButtonProps) {
  return (
    <button
      className={clsx(
        'px-4 py-2 rounded-md font-medium transition-colors',
        {
          'bg-blue-500 text-white hover:bg-blue-600': variant === 'primary',
          'bg-slate-200 text-slate-900 hover:bg-slate-300': variant === 'secondary',
          'opacity-50 cursor-not-allowed': isLoading,
        }
      )}
    >
      {isLoading ? 'Loading...' : 'Click me'}
    </button>
  )
}
```

---

## CUSTOM UTILITIES (RARELY NEEDED)

Tailwind provides 95% of utilities. Only create custom ones for unique needs:

```css
/* globals.css */

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer utilities {
  /* Custom: center content with max-width */
  .center-container {
    @apply mx-auto px-4 max-w-6xl;
  }

  /* Custom: smooth focus ring */
  .focus-ring {
    @apply outline-none ring-2 ring-offset-2 ring-blue-500;
  }

  /* Custom: truncate at 2 lines */
  .line-clamp-2 {
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
  }
}
```

Then use:

```tsx
<div class="center-container">
  {/* Content */}
</div>

<button class="focus-ring">
  {/* Button */}
</button>
```

---

## CSS VARIABLE INTEGRATION

Tailwind + CSS variables for runtime theming:

```css
/* styles/globals.css */

:root {
  --color-primary-500: #3b82f6;
  --color-primary-600: #2563eb;
  --color-primary-700: #1d4ed8;

  --color-slate-900: #0f172a;
  --color-slate-600: #475569;

  /* Semantic surfaces (from LAYER-01) */
  --surface-background: #f8fafc;
  --surface-1: #ffffff;
  --surface-2: #f1f5f9;
  --surface-text-primary: #0f172a;
  --surface-border-subtle: #e2e8f0;
}

@supports (selector(:theme-dark)) {
  :root:theme-dark {
    --color-primary-500: #60a5fa;
    --color-primary-600: #3b82f6;
    --color-primary-700: #2563eb;

    --color-slate-900: #e2e8f0;
    --color-slate-600: #cbd5e1;

    --surface-background: #0f172a;
    --surface-1: #1e293b;
    --surface-2: #334155;
    --surface-text-primary: #f8fafc;
    --surface-border-subtle: #334155;
  }
}
```

Use in components:

```tsx
<button style={{ backgroundColor: 'var(--color-primary-500)' }}>
  Theme-aware button
</button>

// Or with Tailwind (preferred):
<button class="bg-primary-500">
  Primary button
</button>
```

---

## PERFORMANCE OPTIMIZATION

### 1. PurgeCSS (Automatic)
Tailwind automatically removes unused CSS in production:

```typescript
// tailwind.config.ts
content: [
  './src/pages/**/*.{js,ts,jsx,tsx}',
  './src/components/**/*.{js,ts,jsx,tsx}',
  './src/app/**/*.{js,ts,jsx,tsx}',
],
```

**Result:**
- Development: ~350KB (all utilities)
- Production: ~15KB (used utilities only)

### 2. Critical CSS
For above-the-fold rendering:

```html
<!-- Inline critical CSS in head -->
<style>
  /* Only utilities used on initial render */
</style>
```

### 3. CSS Splitting
Use CSS modules for component-specific styles (rare):

```tsx
// Button.module.css
.button {
  @apply px-4 py-2 rounded-md;
}

// Button.tsx
import styles from './Button.module.css'

export function Button() {
  return <button className={styles.button} />
}
```

### 4. Font Optimization

```typescript
// tailwind.config.ts
theme: {
  fontFamily: {
    sans: ['Inter', ...defaultTheme.fontFamily.sans],
  },
}
```

```html
<!-- Use system fonts as fallback -->
<link
  href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap"
  rel="stylesheet"
/>
```

---

## DARK MODE SUPPORT

### Class Strategy

```typescript
// tailwind.config.ts
darkMode: 'class',
```

```html
<!-- Add dark class to html -->
<html class="dark">
  <body class="bg-white dark:bg-slate-900">
    {/* Content */}
  </body>
</html>
```

### Usage

```tsx
<div class="bg-white dark:bg-slate-900 text-slate-900 dark:text-slate-100">
  {/* Automatically adapts to dark mode */}
</div>
```

### Toggle Implementation

```tsx
export function DarkModeToggle() {
  const [isDark, setIsDark] = useState(false)

  const toggle = () => {
    setIsDark(!isDark)
    if (isDark) {
      document.documentElement.classList.remove('dark')
    } else {
      document.documentElement.classList.add('dark')
    }
  }

  return (
    <button onClick={toggle}>
      {isDark ? '☀️' : '🌙'}
    </button>
  )
}
```

---

## PRODUCTION CHECKLIST

- ✅ `tailwind.config.ts` content paths are complete
- ✅ Unused CSS is purged (verify build size)
- ✅ Custom utilities are in `@layer utilities`
- ✅ Dark mode is implemented if needed
- ✅ Fonts are optimized and self-hosted if possible
- ✅ CSS is minified in production
- ✅ No inline `<style>` tags in production

---

## NEXT STEPS

1. **Set up Tailwind:** Follow official [Tailwind CSS installation guide](https://tailwindcss.com/docs/installation)
2. **Configure:** Update `tailwind.config.ts` with tokens from Layer 1
3. **Read [LAYER-05-RESPONSIVE-DESIGN.md](./LAYER-05-RESPONSIVE-DESIGN.md)** for responsive patterns
