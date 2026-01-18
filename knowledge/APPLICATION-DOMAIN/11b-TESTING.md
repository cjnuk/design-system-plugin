---
title: "11b: Testing Strategy"
version: "2.1"
audience: ["developers", "qa", "llm-agents"]
dependencies: ["LAYER-08-ACCESSIBILITY"]
last_updated: "2026-01-18"
---

# 11b: TESTING STRATEGY

**Purpose:** Complete testing approach for applications using shadcn/ui.

---

## TESTING PYRAMID

```
         ┌─────────┐
         │   E2E   │  20% - Full user journeys
         │ (slow)  │
         └────┬────┘
              │
    ┌─────────┴─────────┐
    │   Integration      │  40% - Feature interactions
    │   (medium speed)   │
    └─────────┬─────────┘
              │
┌─────────────┴─────────────┐
│       Unit Tests           │  40% - Individual components
│       (fast)               │
└───────────────────────────┘
```

---

## TOOLS

| Tool | Purpose |
|------|---------|
| **Vitest** | Unit/integration test runner (fast) |
| **React Testing Library** | Component testing |
| **jest-axe** | Accessibility testing |
| **Playwright** | E2E testing |
| **MSW** | API mocking |

---

## SETUP

```bash
# Install testing dependencies
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jest-axe @vitejs/plugin-react jsdom
npm install -D @playwright/test
npm install -D msw
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    setupFiles: ['./src/test/setup.ts'],
    globals: true,
  },
})
```

```typescript
// src/test/setup.ts
import '@testing-library/jest-dom'
import { expect } from 'vitest'
import { toHaveNoViolations } from 'jest-axe'

expect.extend(toHaveNoViolations)
```

---

## UNIT TESTING COMPONENTS

### Basic Component Test

```typescript
// components/ui/button.test.tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { describe, test, expect, vi } from 'vitest'
import { Button } from './button'

describe('Button', () => {
  test('renders with text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button', { name: 'Click me' })).toBeInTheDocument()
  })

  test('handles click events', async () => {
    const user = userEvent.setup()
    const onClick = vi.fn()
    
    render(<Button onClick={onClick}>Click</Button>)
    await user.click(screen.getByRole('button'))
    
    expect(onClick).toHaveBeenCalledOnce()
  })

  test('renders disabled state', () => {
    render(<Button disabled>Disabled</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })

  test('renders different variants', () => {
    const { rerender } = render(<Button variant="default">Default</Button>)
    expect(screen.getByRole('button')).toHaveClass('bg-primary')
    
    rerender(<Button variant="destructive">Destructive</Button>)
    expect(screen.getByRole('button')).toHaveClass('bg-destructive')
  })
})
```

### Accessibility Test

```typescript
// components/ui/button.test.tsx
import { axe } from 'jest-axe'

test('has no accessibility violations', async () => {
  const { container } = render(<Button>Accessible Button</Button>)
  const results = await axe(container)
  expect(results).toHaveNoViolations()
})
```

---

## TESTING FORMS

```typescript
// components/features/login-form.test.tsx
import { render, screen, waitFor } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { describe, test, expect, vi } from 'vitest'
import { LoginForm } from './login-form'

describe('LoginForm', () => {
  test('submits with valid data', async () => {
    const user = userEvent.setup()
    const onSubmit = vi.fn()
    
    render(<LoginForm onSubmit={onSubmit} />)
    
    await user.type(screen.getByLabelText(/email/i), 'test@example.com')
    await user.type(screen.getByLabelText(/password/i), 'password123')
    await user.click(screen.getByRole('button', { name: /log in/i }))
    
    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({
        email: 'test@example.com',
        password: 'password123',
      })
    })
  })

  test('shows validation error for invalid email', async () => {
    const user = userEvent.setup()
    
    render(<LoginForm onSubmit={vi.fn()} />)
    
    await user.type(screen.getByLabelText(/email/i), 'invalid-email')
    await user.click(screen.getByRole('button', { name: /log in/i }))
    
    expect(await screen.findByText(/invalid email/i)).toBeInTheDocument()
  })
})
```

---

## TESTING WITH API MOCKING (MSW)

```typescript
// src/test/mocks/handlers.ts
import { http, HttpResponse } from 'msw'

export const handlers = [
  http.get('/api/users', () => {
    return HttpResponse.json([
      { id: '1', name: 'John', email: 'john@example.com' },
      { id: '2', name: 'Jane', email: 'jane@example.com' },
    ])
  }),
  
  http.post('/api/auth/login', async ({ request }) => {
    const body = await request.json()
    if (body.email === 'test@example.com') {
      return HttpResponse.json({ id: '1', name: 'Test User' })
    }
    return new HttpResponse(null, { status: 401 })
  }),
]
```

```typescript
// src/test/setup.ts
import { setupServer } from 'msw/node'
import { handlers } from './mocks/handlers'

export const server = setupServer(...handlers)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

---

## E2E TESTING (PLAYWRIGHT)

```typescript
// e2e/login.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Login Flow', () => {
  test('successful login redirects to dashboard', async ({ page }) => {
    await page.goto('/login')
    
    await page.fill('input[name="email"]', 'test@example.com')
    await page.fill('input[name="password"]', 'password123')
    await page.click('button[type="submit"]')
    
    await expect(page).toHaveURL('/dashboard')
    await expect(page.locator('h1')).toContainText('Dashboard')
  })

  test('shows error for invalid credentials', async ({ page }) => {
    await page.goto('/login')
    
    await page.fill('input[name="email"]', 'wrong@example.com')
    await page.fill('input[name="password"]', 'wrongpassword')
    await page.click('button[type="submit"]')
    
    await expect(page.locator('[role="alert"]')).toContainText('Invalid credentials')
  })
})
```

---

## COVERAGE TARGETS

| Area | Target |
|------|--------|
| Components | 80% |
| Hooks | 90% |
| Utilities | 90% |
| Pages | 60% |
| E2E Critical Paths | 100% |

---

## RUNNING TESTS

```bash
# Unit/Integration tests
npm run test

# Watch mode
npm run test:watch

# Coverage
npm run test:coverage

# E2E tests
npx playwright test

# E2E with UI
npx playwright test --ui
```

---

**Related:** [09b-COMPONENTS](./09b-COMPONENTS.md) | [12b-IMPLEMENTATION](./12b-IMPLEMENTATION.md)
