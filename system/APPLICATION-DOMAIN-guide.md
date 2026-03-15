# APPLICATION DOMAIN: COMPLETE GUIDE

## File Structure

### APPLICATION-DOMAIN/09b-COMPONENTS.md
**Purpose:** Complete catalog of all shadcn/ui components + custom AI components

For each component:
- Props and API documentation
- Visual variations/states
- Accessibility features
- Code examples
- When to use it
- Common patterns

Components covered:
- Basic: Button, Input, Label, Checkbox, Radio, Toggle, Switch
- Select: Select, Combobox, Command palette
- Forms: Form validation, TextArea, DatePicker
- Dialogs: Dialog, AlertDialog, Drawer, Sheet
- Popovers: Popover, Tooltip, DropdownMenu
- Navigation: Breadcrumb, Pagination, Tabs, Sidebar
- Data: Table, DataTable with sorting/filtering, Tree
- Feedback: Toast, Progress, Badge, Alert
- Layout: Card, Container, Separator, ScrollArea
- Custom AI: ChatWindow, SourcesPanel, MessageBubble, CodeBlock, CiteReference

---

### APPLICATION-DOMAIN/10b-SETUP.md
**Purpose:** Setup and workflow for building SPAs with shadcn/ui

Contents:
- Create Next.js/React project
- Install shadcn/ui CLI
- Run `npx shadcn@latest init`
- Add components as needed: `npx shadcn@latest add button`
- File organization (components/ui/ for base, components/ for custom)
- Customizing components (editing source directly)
- Creating custom components on top of shadcn/ui
- TypeScript setup
- Common customizations

Example:

```bash
# Initialize project
npx create-next-app@latest my-app
cd my-app

# Initialize shadcn/ui
npx shadcn@latest init

# Add components
npx shadcn@latest add button
npx shadcn@latest add input
npx shadcn@latest add form
npx shadcn@latest add table

# Start building!
```

---

### APPLICATION-DOMAIN/11b-TESTING.md
**Purpose:** Complete testing approach for components and features

Testing pyramid:
1. Unit Tests (40%) — Individual component behavior
2. Integration Tests (40%) — Features and component interactions
3. E2E Tests (20%) — Full user journeys

Tools:
- Vitest for unit testing
- React Testing Library for component testing
- jest-axe for accessibility testing
- Playwright for E2E

Coverage targets:
- Components: 80%
- Pages: 60%
- Utilities: 90%

Example test structure:

```jsx
describe('Button Component', () => {
  test('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  test('calls onClick when clicked', () => {
    const onClick = vi.fn()
    render(<Button onClick={onClick}>Click</Button>)
    userEvent.click(screen.getByText('Click'))
    expect(onClick).toHaveBeenCalled()
  })

  test('has no accessibility violations', async () => {
    const { container } = render(<Button>Click</Button>)
    const results = await axe(container)
    expect(results).toHaveNoViolations()
  })
})
```

---

### APPLICATION-DOMAIN/12b-IMPLEMENTATION.md
**Purpose:** Step-by-step guide to building a complete feature

Example: Building a Dashboard

Step-by-step:
1. Design the layout using Grid/Flexbox patterns
2. Build component hierarchy
3. Create data structure
4. Wire up state management (Jotai/Context)
5. Implement API calls (React Query)
6. Add loading states (skeletons)
7. Add error handling (toast notifications)
8. Implement real-time updates
9. Write unit tests
10. Write integration tests
11. Performance optimization
12. Accessibility audit

Each section includes:
- Code examples (copy-paste ready)
- Accessibility considerations
- Performance tips
- Testing patterns
- Common mistakes to avoid

---

## QUICK START: SPA APPLICATION

```bash
# 1. Create project
npx create-next-app@latest my-app --typescript

# 2. Initialize shadcn/ui
npx shadcn@latest init

# 3. Add components
npx shadcn@latest add button
npx shadcn@latest add form
npx shadcn@latest add table

# 4. Install additional libraries
npm install jotai @tanstack/react-query

# 5. Build your feature
```

---

## COMPONENT STRUCTURE

```
src/
├── components/
│   ├── ui/              # Base shadcn/ui components
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   ├── form.tsx
│   │   └── ...
│   ├── features/        # Feature-specific components
│   │   ├── dashboard/
│   │   │   ├── Dashboard.tsx
│   │   │   ├── DashboardHeader.tsx
│   │   │   └── DashboardCard.tsx
│   │   ├── forms/
│   │   │   └── LoginForm.tsx
│   │   └── ...
│   └── layouts/         # Layout components
│       ├── MainLayout.tsx
│       └── SidebarLayout.tsx
├── store/              # State management
│   ├── userStore.ts
│   └── appStore.ts
├── hooks/              # Custom hooks
│   ├── useFetch.ts
│   └── useAuth.ts
├── services/           # API services
│   ├── api.ts
│   └── auth.ts
└── pages/              # Route pages
    ├── dashboard.tsx
    └── login.tsx
```

---

## STATE MANAGEMENT EXAMPLE

Using Jotai (recommended for React 19 apps):

```typescript
// atoms/user.ts
import { atom, useAtom, useAtomValue, useSetAtom } from 'jotai'

interface User {
  id: string
  name: string
  email: string
}

// Primitive atoms
export const userAtom = atom<User | null>(null)
export const isLoadingAtom = atom(false)
export const errorAtom = atom<string | null>(null)

// Derived atom
export const isAuthenticatedAtom = atom((get) => get(userAtom) !== null)

// Usage in component
export function useAuth() {
  const [user, setUser] = useAtom(userAtom)
  const [isLoading, setIsLoading] = useAtom(isLoadingAtom)
  const [error, setError] = useAtom(errorAtom)
  
  const login = async (email: string, password: string) => {
    setIsLoading(true)
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify({ email, password }),
      })
      const userData = await response.json()
      setUser(userData)
    } catch (err) {
      setError(err.message)
    } finally {
      setIsLoading(false)
    }
  }
  
  return { user, isLoading, error, login }
}
```

---

## DATA FETCHING WITH REACT QUERY

```typescript
// hooks/useUsers.ts
import { useQuery } from '@tanstack/react-query'

async function fetchUsers() {
  const response = await fetch('/api/users')
  return response.json()
}

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
    staleTime: 1000 * 60 * 5, // 5 minutes
  })
}

// Usage in component
export function UsersList() {
  const { data: users, isLoading, error } = useUsers()

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  )
}
```

---

## TESTING PATTERNS

```typescript
// Button.test.tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { axe, toHaveNoViolations } from 'jest-axe'
import { describe, test, expect, vi } from 'vitest'
import { Button } from './Button'

expect.extend(toHaveNoViolations)

describe('Button', () => {
  test('renders with text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  test('calls onClick handler', async () => {
    const onClick = vi.fn()
    render(<Button onClick={onClick}>Click</Button>)
    await userEvent.click(screen.getByText('Click'))
    expect(onClick).toHaveBeenCalled()
  })

  test('has no accessibility violations', async () => {
    const { container } = render(<Button>Click</Button>)
    const results = await axe(container)
    expect(results).toHaveNoViolations()
  })

  test('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>)
    expect(screen.getByText('Click')).toBeDisabled()
  })
})
```

---

## PERFORMANCE OPTIMIZATION

1. **Code Splitting** — Use dynamic imports for large components
2. **Image Optimization** — Use Next.js Image component
3. **Bundle Analysis** — Use `@next/bundle-analyzer`
4. **Lazy Loading** — Load below-the-fold content on demand
5. **Caching** — Use React Query's staleTime and gcTime
6. **Memoization** — Use React.memo and useMemo strategically
7. **CSS Purging** — Tailwind automatically purges unused classes

Example:

```typescript
// Dynamic import
const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <Skeleton />,
})

// Image optimization
import Image from 'next/image'

<Image
  src="/my-image.jpg"
  alt="My image"
  width={800}
  height={600}
  priority={false}
/>

// Memoization
const MemoizedComponent = React.memo(Component, (prevProps, nextProps) => {
  return prevProps.id === nextProps.id // Return true if equal
})
```

---

## DEPLOYMENT CHECKLIST

- [ ] All tests passing
- [ ] No console errors/warnings
- [ ] Accessibility audit passed (Lighthouse)
- [ ] Performance audit passed (Lighthouse > 90)
- [ ] Environment variables configured
- [ ] API endpoints verified
- [ ] Error handling tested
- [ ] Loading states tested
- [ ] Dark mode tested (if applicable)
- [ ] Responsive design tested
- [ ] Mobile touch tested
- [ ] Analytics configured
- [ ] Error tracking configured (Sentry)

---

## RESOURCES

- **shadcn/ui Docs:** https://ui.shadcn.com/
- **Radix UI Docs:** https://www.radix-ui.com/docs/primitives/overview/introduction
- **React Query Docs:** https://tanstack.com/query/latest
- **Vitest Docs:** https://vitest.dev/
- **React Testing Library:** https://testing-library.com/react
- **Accessibility Audit:** https://www.w3.org/WAI/test-evaluate/

