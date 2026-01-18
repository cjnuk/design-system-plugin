---
skill: ds-dark-mode
description: Implement dark mode with CSS custom properties and system preference detection
triggers:
  - "add dark mode"
  - "implement dark mode"
  - "ds dark mode"
  - "theme toggle"
  - "dark theme"
---

# Implement Dark Mode

Add dark mode support using CSS custom properties, semantic tokens, and system preference detection. Includes accessible theme toggle.

## Implementation Steps

### 1. CSS Custom Properties (Already in Setup)

If not already configured, add to `globals.css`:

```css
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
```

### 2. Install next-themes

```bash
npm install next-themes
```

### 3. Theme Provider

Create `components/theme-provider.tsx`:

```typescript
"use client"

import * as React from "react"
import { ThemeProvider as NextThemesProvider } from "next-themes"
import { type ThemeProviderProps } from "next-themes/dist/types"

export function ThemeProvider({ children, ...props }: ThemeProviderProps) {
  return <NextThemesProvider {...props}>{children}</NextThemesProvider>
}
```

### 4. Wrap App with Provider

In `app/layout.tsx`:

```typescript
import { ThemeProvider } from "@/components/theme-provider"

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body>
        <ThemeProvider
          attribute="class"
          defaultTheme="system"
          enableSystem
          disableTransitionOnChange
        >
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}
```

### 5. Theme Toggle Component

Create `components/theme-toggle.tsx`:

```typescript
"use client"

import * as React from "react"
import { Moon, Sun } from "lucide-react"
import { useTheme } from "next-themes"
import { Button } from "@/components/ui/button"
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from "@/components/ui/dropdown-menu"

export function ThemeToggle() {
  const { setTheme } = useTheme()

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="ghost" size="icon" aria-label="Toggle theme">
          <Sun className="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">Toggle theme</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme("light")}>
          Light
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("dark")}>
          Dark
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme("system")}>
          System
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  )
}
```

### 6. Simple Toggle (Alternative)

```typescript
"use client"

import * as React from "react"
import { Moon, Sun } from "lucide-react"
import { useTheme } from "next-themes"
import { Button } from "@/components/ui/button"

export function ThemeToggle() {
  const { theme, setTheme } = useTheme()

  return (
    <Button
      variant="ghost"
      size="icon"
      onClick={() => setTheme(theme === "light" ? "dark" : "light")}
      aria-label={`Switch to ${theme === "light" ? "dark" : "light"} mode`}
    >
      <Sun className="h-[1.2rem] w-[1.2rem] rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
      <Moon className="absolute h-[1.2rem] w-[1.2rem] rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
    </Button>
  )
}
```

## Using Semantic Tokens

### Always Use Semantic Tokens

```typescript
// NEVER hardcode colors
<div className="bg-white dark:bg-slate-900">

// ALWAYS use semantic tokens
<div className="bg-background">
```

### Token Mapping

| Token | Light | Dark |
|-------|-------|------|
| `bg-background` | White | Slate 900 |
| `bg-card` | White | Slate 800 |
| `bg-muted` | Slate 100 | Slate 700 |
| `text-foreground` | Slate 900 | Slate 50 |
| `text-muted-foreground` | Slate 500 | Slate 400 |
| `border-border` | Slate 200 | Slate 700 |

## Dark Mode Images

```typescript
// Different images for light/dark
<picture>
  <source
    srcSet="/images/hero-dark.png"
    media="(prefers-color-scheme: dark)"
  />
  <img src="/images/hero-light.png" alt="Hero" />
</picture>

// Or with next/image and CSS
<div className="relative">
  <Image
    src="/images/hero-light.png"
    alt="Hero"
    className="dark:hidden"
  />
  <Image
    src="/images/hero-dark.png"
    alt="Hero"
    className="hidden dark:block"
  />
</div>
```

## Prevent Flash of Wrong Theme

Add to `<head>` in `layout.tsx` or use next-themes which handles this:

```html
<script>
  (function() {
    const theme = localStorage.getItem('theme') ||
      (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
    document.documentElement.classList.toggle('dark', theme === 'dark');
  })();
</script>
```

With next-themes, use `suppressHydrationWarning` on `<html>`:

```typescript
<html lang="en" suppressHydrationWarning>
```

## Contrast Verification

Both light and dark themes must meet WCAG 2.1 AA contrast ratios:

| Element | Minimum Ratio |
|---------|---------------|
| Normal text | 4.5:1 |
| Large text (18px+) | 3:1 |
| UI components | 3:1 |

Test with [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/).

## Validation Checklist

- [ ] All semantic tokens have dark mode variants
- [ ] No hardcoded colors (e.g., `bg-white`, `text-slate-900`)
- [ ] Contrast ratios meet WCAG 2.1 AA in both modes
- [ ] System preference is respected on first load
- [ ] User preference persists across sessions
- [ ] Toggle is keyboard accessible
- [ ] Toggle has appropriate `aria-label`
- [ ] No flash of wrong theme on page load
- [ ] Images/icons work in both modes
