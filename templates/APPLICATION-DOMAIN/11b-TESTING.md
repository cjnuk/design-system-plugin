# Testing Strategy

Project-specific testing approach and patterns.

## Unit Testing

### Framework: Vitest

**Setup**:
```bash
npm install -D vitest @vitest/ui @testing-library/react @testing-library/jest-dom
```

**vitest.config.ts**:
```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './vitest.setup.ts',
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```

**Testing Patterns**:

1. **Component Tests**: Test component behavior, props, events
2. **Hook Tests**: Test custom React hooks with `renderHook`
3. **Utility Tests**: Test helper functions
4. **Accessibility Tests**: Use `jest-axe` for a11y checks

**Example Test**:
```typescript
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Button } from '@/components/ui/button'

describe('Button', () => {
  it('renders with default variant', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button')).toBeInTheDocument()
  })

  it('handles click events', async () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>Click me</Button>)
    await userEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalled()
  })
})
```

## End-to-End Testing

### Framework: Playwright

**Setup**:
```bash
npm install -D @playwright/test
npx playwright install
```

**playwright.config.ts**:
```typescript
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'npm run dev',
    port: 3000,
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
  ],
})
```

**E2E Test Example**:
```typescript
import { test, expect } from '@playwright/test'

test('user can create a new task', async ({ page }) => {
  await page.goto('/')
  await page.fill('[aria-label="Task input"]', 'New task')
  await page.click('text=Add')
  await expect(page.locator('text=New task')).toBeVisible()
})
```

## Test Coverage

### Target Coverage

- Unit: 80% for components, 90% for utilities
- E2E: Critical user journeys

### Running Tests

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "test:e2e:debug": "playwright test --debug"
  }
}
```

## Accessibility Testing

Use `jest-axe` in component tests:

```typescript
import { axe, toHaveNoViolations } from 'jest-axe'

expect.extend(toHaveNoViolations)

it('has no accessibility violations', async () => {
  const { container } = render(<Button>Click me</Button>)
  const results = await axe(container)
  expect(results).toHaveNoViolations()
})
```

## Code Coverage Requirements

Before shipping components:

1. Unit tests pass (100% for critical paths)
2. No accessibility violations detected
3. E2E tests verify user workflows
4. Visual regression tests pass (if using Percy, Chromatic, etc.)

## Testing Patterns from Design System

See LAYER-08-ACCESSIBILITY for testing requirements by component type.
