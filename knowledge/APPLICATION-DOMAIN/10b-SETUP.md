---
title: "10b: Application Setup & Build"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["LAYER-04-CSS-ARCHITECTURE"]
last_updated: "2026-01-18"
---

# 10b: APPLICATION SETUP & BUILD

**Purpose:** Step-by-step setup guide for SPA applications using shadcn/ui.

---

## QUICK START

```bash
# 1. Create Next.js project with App Router
npx create-next-app@latest my-app --typescript --tailwind --app

# 2. Navigate to project
cd my-app

# 3. Initialize shadcn/ui
npx shadcn@latest init

# 4. Add core components
npx shadcn@latest add button input form dialog toast

# 5. Install additional dependencies
npm install zustand @tanstack/react-query

# 6. Start development
npm run dev
```

---

## SHADCN/UI INITIALIZATION

When running `npx shadcn@latest init`, you'll be asked:

```
Which style would you like to use? › Default
Which color would you like to use as base color? › Slate
Do you want to use CSS variables for colors? › Yes
```

This creates:
- `components.json` — shadcn configuration
- `lib/utils.ts` — `cn()` utility function
- `tailwind.config.ts` — Updated with shadcn config

---

## PROJECT STRUCTURE

```
my-app/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── (routes)/
│   │       ├── dashboard/page.tsx
│   │       └── settings/page.tsx
│   ├── components/
│   │   ├── ui/                  # shadcn/ui components
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   └── ...
│   │   ├── features/            # Feature components
│   │   │   └── dashboard/
│   │   └── layout/              # Layout components
│   │       ├── header.tsx
│   │       └── sidebar.tsx
│   ├── lib/
│   │   ├── utils.ts             # Utility functions
│   │   └── api.ts               # API client
│   ├── hooks/                   # Custom hooks
│   │   └── use-auth.ts
│   └── store/                   # State management
│       └── user-store.ts
├── components.json
├── tailwind.config.ts
└── package.json
```

---

## TAILWIND CONFIGURATION (WITH SHADCN)

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config = {
  darkMode: ['class'],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    container: {
      center: true,
      padding: '2rem',
      screens: {
        '2xl': '1400px',
      },
    },
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))',
        },
        // ... more semantic colors
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
} satisfies Config

export default config
```

---

## STATE MANAGEMENT SETUP (ZUSTAND)

```typescript
// src/store/user-store.ts
import { create } from 'zustand'

interface User {
  id: string
  name: string
  email: string
}

interface UserStore {
  user: User | null
  isLoading: boolean
  setUser: (user: User | null) => void
  login: (email: string, password: string) => Promise<void>
  logout: () => void
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  isLoading: false,
  
  setUser: (user) => set({ user }),
  
  login: async (email, password) => {
    set({ isLoading: true })
    try {
      const res = await fetch('/api/auth/login', {
        method: 'POST',
        body: JSON.stringify({ email, password }),
      })
      const user = await res.json()
      set({ user, isLoading: false })
    } catch {
      set({ isLoading: false })
      throw new Error('Login failed')
    }
  },
  
  logout: () => set({ user: null }),
}))
```

---

## DATA FETCHING SETUP (REACT QUERY)

```typescript
// src/app/providers.tsx
'use client'

import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function Providers({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000, // 1 minute
      },
    },
  }))

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}
```

```typescript
// src/hooks/use-users.ts
import { useQuery } from '@tanstack/react-query'

async function fetchUsers() {
  const res = await fetch('/api/users')
  return res.json()
}

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
  })
}
```

---

## BUILD & DEPLOY

```bash
# Type check
npx tsc --noEmit

# Lint
npm run lint

# Build
npm run build

# Deploy to Vercel
vercel deploy --prod
```

---

**Related:** [09b-COMPONENTS](./09b-COMPONENTS.md) | [11b-TESTING](./11b-TESTING.md)
