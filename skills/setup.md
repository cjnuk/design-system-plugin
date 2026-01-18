---
skill: ds-setup
description: Initialize a project with design system tokens, dependencies, and shadcn/ui configuration
triggers:
  - "set up design system"
  - "initialize design system"
  - "ds setup"
  - "configure shadcn"
  - "install design system"
---

# Design System Setup

Initialize a Next.js or React project with the design system's tokens, dependencies, and shadcn/ui configuration.

## What This Does

1. Installs required dependencies (shadcn/ui, class-variance-authority, clsx, tailwind-merge)
2. Configures Tailwind CSS with semantic tokens
3. Sets up the `cn()` utility function
4. Initializes shadcn/ui with proper theming
5. Creates the CSS custom properties for light/dark mode

## Prerequisites

- Node.js 18+
- A Next.js or Vite React project
- Tailwind CSS already installed

## Setup Steps

### 1. Install Dependencies

```bash
npm install class-variance-authority clsx tailwind-merge
npx shadcn@latest init
```

### 2. Create Utils

Create `src/lib/utils.ts`:

```typescript
import { type ClassValue, clsx } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

### 3. Configure CSS Custom Properties

Add to `src/app/globals.css` (or your main CSS file):

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;
    --primary: 221.2 83.2% 53.3%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 221.2 83.2% 53.3%;
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;
    --primary: 217.2 91.2% 59.8%;
    --primary-foreground: 222.2 47.4% 11.2%;
    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;
    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;
    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;
    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;
    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 224.3 76.3% 48%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### 4. Update Tailwind Config

Update `tailwind.config.ts`:

```typescript
import type { Config } from "tailwindcss"

const config: Config = {
  darkMode: ["class"],
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}

export default config
```

### 5. Add Core Components

```bash
npx shadcn@latest add button input label
npx shadcn@latest add form
npx shadcn@latest add dialog card
```

## Validation Checklist

After setup, verify:

- [ ] `cn()` utility works in components
- [ ] Semantic tokens resolve correctly (test with `bg-primary`)
- [ ] Dark mode toggle changes colors
- [ ] shadcn/ui components render correctly
- [ ] No hardcoded colors in `tailwind.config.ts`

## Quick Reference

| Token | Light Mode | Dark Mode | Usage |
|-------|------------|-----------|-------|
| `bg-background` | White | Slate 900 | Page background |
| `bg-primary` | Blue 500 | Blue 400 | Primary buttons |
| `text-foreground` | Slate 900 | Slate 50 | Body text |
| `text-muted-foreground` | Slate 500 | Slate 400 | Secondary text |
| `border-border` | Slate 200 | Slate 700 | Borders |

## Next Steps

- Use `/ds-component` to generate new components
- Use `/ds-tokens` for quick token reference
- Use `/ds-review` to validate code against the design system
