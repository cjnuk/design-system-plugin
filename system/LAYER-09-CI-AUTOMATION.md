---
title: "LAYER 09: CI Automation"
version: "2.1"
audience: ["devops", "developers", "llm-agents"]
dependencies: ["LAYER-00-STRATEGIC-DECISIONS"]
last_updated: "2026-01-18"
---

# LAYER 09: CI AUTOMATION

**Purpose:** Automated quality checks for the design system documentation and generated artifacts.

---

## OVERVIEW

This layer defines CI/CD pipelines for:
1. **Documentation validation** — Markdown linting, link checking
2. **Storybook** — Component documentation and visual testing
3. **TypeDoc** — API documentation generation
4. **Visual regression** — Chromatic or Percy integration

---

## DOCUMENTATION CI

### Markdown Linting

```yaml
# .github/workflows/design-system.yml
name: Design System CI

on:
  push:
    branches: [main]
    paths: ['system/**', 'docs/**']
  pull_request:
    paths: ['system/**', 'docs/**']

jobs:
  lint-markdown:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Lint Markdown
        uses: DavidAnson/markdownlint-cli2-action@v18
        with:
          globs: 'system/**/*.md'
          config: '.markdownlint.json'
```

### Link Checking

```yaml
  check-links:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check Links
        uses: lycheeverse/lychee-action@v2
        with:
          args: --verbose --no-progress 'system/**/*.md'
          fail: true
```

### Markdownlint Config

```json
// .markdownlint.json
{
  "default": true,
  "MD013": false,
  "MD033": false,
  "MD041": false,
  "MD024": { "siblings_only": true }
}
```

---

## STORYBOOK

### Setup

```bash
# Initialize Storybook (if not already set up)
npx storybook@latest init

# Install addons
npm install -D @storybook/addon-a11y @storybook/addon-designs
```

### Configuration

```typescript
// .storybook/main.ts
import type { StorybookConfig } from '@storybook/react-vite'

const config: StorybookConfig = {
  stories: ['../src/components/**/*.stories.@(js|jsx|ts|tsx|mdx)'],
  addons: [
    '@storybook/addon-essentials',
    '@storybook/addon-a11y',
    '@storybook/addon-designs',
  ],
  framework: {
    name: '@storybook/react-vite',
    options: {},
  },
}

export default config
```

### CI for Storybook

```yaml
  build-storybook:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Build Storybook
        run: npm run build-storybook

      - name: Upload Storybook
        uses: actions/upload-artifact@v4
        with:
          name: storybook
          path: storybook-static
```

---

## TYPEDOC

### Setup

```bash
npm install -D typedoc typedoc-plugin-markdown
```

### Configuration

```json
// typedoc.json
{
  "entryPoints": ["src/components/ui/index.ts"],
  "out": "docs/api",
  "plugin": ["typedoc-plugin-markdown"],
  "readme": "none",
  "excludePrivate": true,
  "excludeProtected": true
}
```

### CI for TypeDoc

```yaml
  generate-api-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Generate TypeDoc
        run: npx typedoc

      - name: Upload API Docs
        uses: actions/upload-artifact@v4
        with:
          name: api-docs
          path: docs/api
```

---

## VISUAL REGRESSION (CHROMATIC)

### Setup

```bash
npm install -D chromatic
```

### CI Integration

```yaml
  visual-regression:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Publish to Chromatic
        uses: chromaui/action@latest
        with:
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          buildScriptName: build-storybook
```

---

## COMPLETE WORKFLOW

See `.github/workflows/design-system.yml` for the full implementation.

### Job Dependencies

```
┌─────────────────┐
│  lint-markdown  │
└────────┬────────┘
         │
┌────────┴────────┐
│   check-links   │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───┴───┐ ┌───┴────────────┐
│ build │ │ generate-docs  │
│ story │ │ (TypeDoc)      │
│ book  │ └────────────────┘
└───┬───┘
    │
┌───┴─────────────┐
│ visual-regression│
│ (Chromatic)      │
└─────────────────┘
```

---

## LOCAL SCRIPTS

Add these to `package.json`:

```json
{
  "scripts": {
    "lint:md": "markdownlint-cli2 'system/**/*.md'",
    "lint:links": "lychee 'system/**/*.md'",
    "docs:api": "typedoc",
    "storybook": "storybook dev -p 6006",
    "build-storybook": "storybook build"
  }
}
```

---

## DEPLOYMENT

### GitHub Pages

```yaml
  deploy-docs:
    needs: [build-storybook, generate-api-docs]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    permissions:
      pages: write
      id-token: write
    
    steps:
      - name: Download Storybook
        uses: actions/download-artifact@v4
        with:
          name: storybook
          path: public/storybook

      - name: Download API Docs
        uses: actions/download-artifact@v4
        with:
          name: api-docs
          path: public/api

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

---

**Related:** [LAYER-00](./LAYER-00-STRATEGIC-DECISIONS.md) | [APPLICATION-DOMAIN/11b-TESTING](./APPLICATION-DOMAIN/11b-TESTING.md)
