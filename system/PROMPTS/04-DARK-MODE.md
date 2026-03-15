---
title: "Prompt: Implement Dark Mode"
task_type: "theming"
retrieval_path: ["LLM-AGENT-GUIDE", "LAYER-01-DESIGN-TOKENS", "APPLICATION-DOMAIN/10b-SETUP"]
---

# PROMPT: IMPLEMENT DARK MODE

Use this prompt when asking an LLM to add or fix dark mode support.

---

## PROMPT TEMPLATE

```
Implement dark mode for [COMPONENT/PAGE/APP].

Current state: [Describe current theming]
Requirements:
- [List specific dark mode requirements]

Technical constraints:
- Use CSS custom properties with .dark class
- Follow semantic token naming from LAYER-01-DESIGN-TOKENS
- Support system preference detection
- Persist user preference to localStorage
- Ensure smooth transitions

Reference files:
- system/LAYER-01-DESIGN-TOKENS.md for token definitions
- system/APPLICATION-DOMAIN/10b-SETUP.md for theme setup
```

---

## EXAMPLE USAGE

```
Implement dark mode for the dashboard layout.

Current state: Only light theme defined
Requirements:
- Toggle button in header
- Respect system preference initially
- Remember user choice
- Smooth color transitions

Technical constraints:
- Use CSS custom properties with .dark class
- Follow semantic token naming from LAYER-01-DESIGN-TOKENS
- Support system preference detection
- Persist user preference to localStorage
- Ensure smooth transitions
```

---

## IMPLEMENTATION PATTERN

### 1. CSS Variables (globals.css)

```css
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    /* ... other tokens */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    /* ... other tokens */
  }
}

/* Smooth transitions */
* {
  @apply transition-colors duration-200;
}
```

### 2. Theme Provider

```typescript
// components/theme-provider.tsx
"use client"

import { createContext, useContext, useEffect, useState } from "react"

type Theme = "light" | "dark" | "system"

interface ThemeContextValue {
  theme: Theme
  setTheme: (theme: Theme) => void
}

const ThemeContext = createContext<ThemeContextValue | null>(null)

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<Theme>("system")

  useEffect(() => {
    const stored = localStorage.getItem("theme") as Theme | null
    if (stored) setTheme(stored)
  }, [])

  useEffect(() => {
    const root = document.documentElement
    root.classList.remove("light", "dark")

    if (theme === "system") {
      const systemTheme = window.matchMedia("(prefers-color-scheme: dark)").matches
        ? "dark"
        : "light"
      root.classList.add(systemTheme)
    } else {
      root.classList.add(theme)
    }

    localStorage.setItem("theme", theme)
  }, [theme])

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}

export function useTheme() {
  const ctx = useContext(ThemeContext)
  if (!ctx) throw new Error("useTheme must be used within ThemeProvider")
  return ctx
}
```

### 3. Theme Toggle

```typescript
// components/theme-toggle.tsx
"use client"

import { Moon, Sun } from "lucide-react"
import { Button } from "@/components/ui/button"
import { useTheme } from "@/components/theme-provider"

export function ThemeToggle() {
  const { theme, setTheme } = useTheme()

  return (
    <Button
      variant="ghost"
      size="icon"
      onClick={() => setTheme(theme === "dark" ? "light" : "dark")}
      aria-label={`Switch to ${theme === "dark" ? "light" : "dark"} mode`}
    >
      <Sun className="h-5 w-5 rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
      <Moon className="absolute h-5 w-5 rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
    </Button>
  )
}
```

---

## VALIDATION CHECKLIST

- [ ] All semantic tokens have dark variants
- [ ] No hardcoded colors (use `bg-background`, `text-foreground`, etc.)
- [ ] Contrast ratios meet WCAG 2.1 AA in both modes
- [ ] System preference is respected on first load
- [ ] User preference persists across sessions
- [ ] Toggle is accessible (has label, keyboard operable)
