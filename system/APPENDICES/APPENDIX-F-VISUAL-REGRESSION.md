---
title: "Appendix F: Visual Regression Testing"
version: "1.0"
audience: ["developers", "qa-engineers", "llm-agents"]
dependencies: ["LAYER-09-CI-AUTOMATION", "LAYER-02-COMPONENTS"]
last_updated: "2026-02-04"
---

# APPENDIX F: VISUAL REGRESSION TESTING

**Purpose:** Comprehensive guide to setting up and maintaining visual regression testing for design system components.

---

## OVERVIEW

Visual regression testing automatically detects unintended UI changes by comparing component screenshots across commits. This prevents visual bugs from reaching production while maintaining design consistency.

### Key Concepts

```
Component Baseline (reference screenshots)
    ↓
Automated Test Run (new screenshots)
    ↓
Pixel Comparison (diff detection)
    ↓
Review & Approve (human validation)
```

### When to Use

- After component style changes
- Before merging design system updates
- During theme/token modifications
- Validating responsive breakpoints
- Testing dark mode variants

---

## CHROMATIC (Recommended)

Chromatic is a cloud-based visual testing platform built specifically for Storybook.

### Setup

#### 1. Install Chromatic

```bash
npm install -D chromatic
```

#### 2. Create Stories

Ensure components have Storybook stories (`.stories.tsx`):

```typescript
import { Meta, StoryObj } from '@storybook/react'
import { Button } from './Button'

const meta: Meta<typeof Button> = {
  component: Button,
  title: 'Components/Button',
  argTypes: {
    variant: {
      control: { type: 'radio' },
      options: ['primary', 'secondary', 'ghost'],
    },
  },
}

export default meta
type Story = StoryObj<typeof Button>

export const Primary: Story = {
  args: { variant: 'primary', children: 'Click me' },
}

export const Secondary: Story = {
  args: { variant: 'secondary', children: 'Secondary' },
}

export const Hover: Story = {
  args: { variant: 'primary', children: 'Hover state' },
  parameters: {
    pseudo: { hover: true },
  },
}

export const Dark: Story = {
  args: { variant: 'primary', children: 'Dark mode' },
  parameters: {
    backgrounds: { default: 'dark' },
  },
}
```

#### 3. Configure Chromatic in `.github/workflows/chromatic.yml`

```yaml
name: Chromatic

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  chromatic:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run Chromatic
        uses: chromaui/action@v1
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          token: ${{ secrets.GITHUB_TOKEN }}
          exitOnceUploaded: true
          onlyChanged: true
          storybookBuildDir: storybook-static
```

#### 4. Get Project Token

```bash
# Visit https://www.chromatic.com/ and create account
# Generate token and add to GitHub Secrets as CHROMATIC_PROJECT_TOKEN
```

### Configuration

#### Chromatic Configuration File (`chromatic.config.json`)

```json
{
  "projectToken": "YOUR_TOKEN_HERE",
  "autoAcceptChanges": "main",
  "ignoreLastBuildOnBranch": ["dependabot/**"],
  "externals": [
    "public/**",
    "node_modules/**"
  ],
  "onlyChanged": true,
  "skip": false
}
```

#### Advanced Options

| Option | Default | Purpose |
|--------|---------|---------|
| `autoAcceptChanges` | `main` | Auto-accept changes on specified branches |
| `onlyChanged` | `true` | Only test stories changed in PR |
| `exitZeroOnChanges` | `false` | Exit 0 even if changes detected (for gentle CI) |
| `skipCheckout` | `false` | Skip git checkout (monorepo optimization) |
| `externals` | `[]` | Paths to ignore when detecting changes |

#### Ignoring Elements

```typescript
// Story with ignored elements
export const WithDynamic: Story = {
  args: { children: 'Content' },
  parameters: {
    chromatic: {
      disableSnapshot: false,
      ignore: ['.dynamic-timestamp', '.animated-loading'],
    },
  },
}
```

### Handling Dynamic Content

#### Dates & Timestamps

```typescript
// Mock current time
export const CurrentDate: Story = {
  args: { date: new Date('2026-02-04T10:00:00Z') },
  parameters: {
    chromatic: { pauseAnimationAtEnd: true },
  },
}

// Or disable snapshot for time-based components
export const RealTime: Story = {
  parameters: {
    chromatic: { disableSnapshot: true },
  },
}
```

#### Avatars & Images

```typescript
// Use consistent placeholder images
export const WithAvatar: Story = {
  args: {
    avatarUrl: 'https://i.pravatar.cc/150?u=consistent@example.com',
  },
}

// Or mock image loading
export const ImageLoading: Story = {
  parameters: {
    chromatic: { pauseAnimationAtEnd: true },
  },
}
```

#### Animations

```typescript
// Pause animations at end state
export const Animated: Story = {
  args: { animate: true },
  parameters: {
    chromatic: { pauseAnimationAtEnd: true },
  },
}

// Or disable snapshot entirely
export const RealAnimation: Story = {
  parameters: {
    chromatic: { disableSnapshot: true },
  },
}
```

### Best Practices

1. **Story Variants:** Create stories for all component states (normal, hover, active, disabled)
2. **Responsive:** Use viewport parameter for mobile/tablet/desktop sizes
3. **Dark Mode:** Include dark mode variants in parameters
4. **Consistent Sizes:** Use fixed sizes for dynamic content (avatars, dates)
5. **Auto-Accept Main:** Only auto-accept on main branch, review all PR changes

---

## PERCY (Alternative)

Percy offers DOM-based visual testing that's language-agnostic.

### Setup

#### 1. Install Percy CLI

```bash
npm install -D @percy/cli @percy/puppeteer
```

#### 2. Create Tests

```typescript
import percySnapshot from '@percy/puppeteer'
import { Page } from 'puppeteer'

describe('Button Component', () => {
  it('should match snapshot - primary variant', async () => {
    const page: Page = /* puppeteer page instance */
    await page.goto('http://localhost:3000/button')
    await percySnapshot(page, 'Button - Primary')
  })

  it('should match snapshot - hover state', async () => {
    const page = await browser.newPage()
    await page.goto('http://localhost:3000/button')
    await page.hover('button')
    await percySnapshot(page, 'Button - Hover')
  })
})
```

#### 3. GitHub Actions Integration

```yaml
name: Percy Snapshots

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - run: npm install

      - name: Percy Snapshot
        run: percy exec -- npm run test:visual
        env:
          PERCY_TOKEN: ${{ secrets.PERCY_TOKEN }}
```

### Configuration

#### Percy Config (`percy.config.js`)

```javascript
module.exports = {
  version: 2,
  static: {
    cleanUrls: true,
    include: '**/*.html',
    exclude: [],
  },
  discovery: {
    allowed-hosts: ['localhost'],
    network-idle-timeout: 750,
  },
  discovery: {
    allowed-hosts: ['localhost'],
  },
}
```

---

## PLAYWRIGHT VISUAL COMPARISON

Lightweight, open-source visual testing using Playwright.

### Setup

#### 1. Install Playwright

```bash
npm install -D @playwright/test
```

#### 2. Configure (`playwright.config.ts`)

```typescript
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './tests',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],
})
```

#### 3. Write Tests (`tests/button.visual.spec.ts`)

```typescript
import { test, expect } from '@playwright/test'

test.describe('Button Visual Tests', () => {
  test('primary button snapshot', async ({ page }) => {
    await page.goto('/components/button?variant=primary')
    await expect(page).toHaveScreenshot('button-primary.png')
  })

  test('button hover state', async ({ page }) => {
    await page.goto('/components/button?variant=primary')
    await page.locator('button').hover()
    await expect(page).toHaveScreenshot('button-hover.png', {
      maxDiffPixels: 100,
    })
  })

  test('button on mobile', async ({ page }) => {
    await page.setViewportSize({ width: 375, height: 667 })
    await page.goto('/components/button?variant=primary')
    await expect(page).toHaveScreenshot('button-mobile.png')
  })
})
```

#### 4. Update Snapshots

```bash
# Generate baseline snapshots
npx playwright test --update-snapshots

# Run tests with strict comparison
npx playwright test

# Update specific test
npx playwright test button.visual.spec.ts --update-snapshots
```

### Threshold Configuration

```typescript
// Customize diff tolerance per test
await expect(page).toHaveScreenshot('dynamic-content.png', {
  maxDiffPixels: 500, // Allow up to 500 pixel differences
  threshold: 0.2,     // Allow up to 20% difference
})
```

---

## THRESHOLD CONFIGURATION

Visual regression tools should be configured with appropriate tolerance levels.

### Chromatic Thresholds

| Scenario | Threshold | Rationale |
|----------|-----------|-----------|
| Typography changes | 0.01 | Strict (font weight, size changes visible immediately) |
| Color updates | 0.05 | Moderate (small rendering differences across browsers) |
| Layout adjustments | 0.1 | Lenient (anti-aliasing varies by platform) |
| Animation states | 0.15 | Lenient (timing/frame differences) |

### Playwright Thresholds

```typescript
// Strict comparison (typography)
maxDiffPixels: 10
threshold: 0.01

// Moderate comparison (colors)
maxDiffPixels: 100
threshold: 0.05

// Lenient comparison (layouts)
maxDiffPixels: 500
threshold: 0.15
```

---

## CI INTEGRATION PATTERNS

### GitHub Actions: Chromatic + Playwright

```yaml
name: Visual & E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  chromatic:
    name: Chromatic Visual Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - run: npm ci

      - name: Build Storybook
        run: npm run build:storybook

      - uses: chromaui/action@v1
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          token: ${{ secrets.GITHUB_TOKEN }}

  playwright:
    name: Playwright Visual Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - run: npm ci

      - run: npx playwright install --with-deps

      - run: npm run test:visual

      - uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
```

### Blocking vs Non-Blocking

#### Blocking (Required to Pass)

```yaml
- name: Chromatic
  uses: chromaui/action@v1
  with:
    exitOnceUploaded: true        # Fail if changes detected
    exitZeroOnChanges: false      # Block merge
```

#### Non-Blocking (Optional Review)

```yaml
- name: Chromatic
  uses: chromaui/action@v1
  continue-on-error: true        # Don't fail the workflow
  with:
    exitZeroOnChanges: true       # Allow merge even with changes
```

---

## BEST PRACTICES

### 1. Story Organization

```
stories/
  components/
    Button/
      Button.stories.tsx          (basic variants)
      Button.interactions.stories.tsx  (hover, focus, active)
      Button.responsive.stories.tsx    (mobile, tablet, desktop)
      Button.states.stories.tsx        (disabled, loading, error)
```

### 2. Naming Conventions

```typescript
// Clear, descriptive story names
export const Primary: Story = { /* ... */ }
export const PrimaryHover: Story = { /* ... */ }
export const PrimaryDisabled: Story = { /* ... */ }
export const OnDarkBackground: Story = { /* ... */ }
export const MobileSmall: Story = { /* ... */ }
```

### 3. Consistent Test Data

```typescript
// Use fixed, consistent data across tests
const MOCK_USER = {
  id: '123',
  name: 'Jane Doe',
  email: 'jane@example.com',
  avatarUrl: 'https://i.pravatar.cc/150?u=jane@example.com',
  createdAt: new Date('2026-01-01'),
}

export const WithUser: Story = {
  args: { user: MOCK_USER },
}
```

### 4. Responsive Breakpoint Coverage

```typescript
// Test all breakpoints
export const MobileXS: Story = {
  parameters: { viewport: { defaultViewport: 'mobile1' } },
}

export const MobileL: Story = {
  parameters: { viewport: { defaultViewport: 'mobileL' } },
}

export const Tablet: Story = {
  parameters: { viewport: { defaultViewport: 'tablet' } },
}

export const Desktop: Story = {
  parameters: { viewport: { defaultViewport: 'desktop' } },
}
```

### 5. Dark Mode Variants

```typescript
export const DarkMode: Story = {
  args: { children: 'Dark theme' },
  parameters: {
    backgrounds: { default: 'dark' },
  },
  decorators: [
    (Story) => (
      <div className="dark bg-slate-900">
        <Story />
      </div>
    ),
  ],
}
```

---

## TROUBLESHOOTING

### Issue: "Snapshot not found"

**Cause:** Test file doesn't have a baseline.

**Solution:**
```bash
# Generate baseline
npx playwright test --update-snapshots

# Or for Chromatic
npm run chromatic -- --auto-accept-changes
```

### Issue: "Flaky tests" (intermittent failures)

**Causes:**
- Animations or transitions incomplete
- Network requests unfinished
- Timing issues with dynamic content

**Solutions:**
```typescript
// Wait for animations to complete
await page.waitForLoadState('networkidle')
await page.locator('button').waitFor({ state: 'stable' })

// Disable animations in tests
await page.addInitScript(() => {
  document.documentElement.style.animation = 'none'
  document.documentElement.style.transition = 'none'
})

// Use specific max-diff-pixels
await expect(page).toHaveScreenshot('test.png', {
  maxDiffPixels: 100,
})
```

### Issue: "CI passes but local fails"

**Cause:** Different OS/browser rendering.

**Solution:**
```bash
# Use docker for consistent environment
docker run --rm -v $(pwd):/work -w /work mcr.microsoft.com/playwright:v1.40.0 npx playwright test

# Or use --update-snapshots on CI
npm run test:visual -- --update-snapshots
```

### Issue: "Anti-aliasing differences"

**Cause:** Font rendering varies by platform.

**Solution:**
```typescript
// Increase threshold tolerance
await expect(page).toHaveScreenshot('text.png', {
  threshold: 0.2,  // Allow 20% variation
})

// Or use SVG text rendering
<text dominant-baseline="middle" text-anchor="middle">
  Fixed text
</text>
```

---

## MAINTENANCE

### Weekly Tasks

1. Review flagged changes in Chromatic dashboard
2. Approve or request modifications
3. Monitor flaky test patterns

### Monthly Tasks

1. Update snapshot baselines for intentional changes
2. Review and optimize story coverage
3. Check for missing responsive variants

### Quarterly Tasks

1. Audit threshold settings
2. Evaluate tool effectiveness
3. Plan new test scenarios

---

## RESOURCES

- **Chromatic Docs:** https://www.chromatic.com/docs
- **Percy Docs:** https://docs.percy.io/
- **Playwright Docs:** https://playwright.dev/docs/test-snapshots
- **Storybook Docs:** https://storybook.js.org/docs/react/

---

**Last Updated:** 2026-02-04
**Author:** Design System Team
