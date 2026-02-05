# Design System Config Files

Shareable configuration files for projects using the design system.

## Overview

| Config File | Purpose | Usage |
|-------------|---------|-------|
| `tsconfig.design-system.json` | Strict TypeScript settings | Extend in `tsconfig.json` |
| `prettierrc.json` | Code formatting rules | Extend in `.prettierrc` or `package.json` |

---

## TypeScript Config

Extend in your `tsconfig.json`:

```json
{
  "extends": "./node_modules/design-system-plugin/configs/tsconfig.design-system.json",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

### Key Strict Settings

| Setting | Purpose |
|---------|---------|
| `strict: true` | Enables all strict type checking options |
| `noUncheckedIndexedAccess` | Forces explicit undefined checks for `arr[i]` |
| `exactOptionalPropertyTypes` | Distinguishes `undefined` from missing properties |
| `noImplicitReturns` | Requires explicit return in all code paths |
| `noFallthroughCasesInSwitch` | Requires break/return in switch cases |

---

## Prettier Config

Standardized code formatting following design system conventions.

### Setup

#### Option A: Extend `prettierrc.json`

```json
{
  "extends": [
    "./node_modules/design-system-plugin/configs/prettierrc.json"
  ]
}
```

#### Option B: Reference in `package.json`

```json
{
  "prettier": "./node_modules/design-system-plugin/configs/prettierrc.json"
}
```

#### Option C: Copy and Customize

```json
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "semi": false,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

### Key Rules

| Setting | Value | Rationale |
|---------|-------|-----------|
| `singleQuote` | `true` | Consistent with modern JavaScript |
| `trailingComma` | `"all"` | Reduces git diffs, easier merges |
| `printWidth` | `100` | Balanced readability for modern displays |
| `tabWidth` | `2` | Standard for Node.js/React projects |
| `semi` | `false` | Cleaner code without unnecessary semicolons |
| `prettier-plugin-tailwindcss` | enabled | Auto-sorts Tailwind classes |

### Installation

```bash
# Install Prettier (if not already present)
npm install -D prettier prettier-plugin-tailwindcss

# Extend or copy the config
cp node_modules/design-system-plugin/configs/prettierrc.json .prettierrc.json
```

### Formatting Commands

```bash
# Format all files
npx prettier --write .

# Format specific directory
npx prettier --write src/

# Check without modifying (CI)
npx prettier --check .

# Format staged files only
npx prettier --write --staged
```

### Integration with ESLint

To avoid conflicts between Prettier and ESLint, install `eslint-config-prettier`:

```bash
npm install -D eslint-config-prettier
```

Then add to your ESLint config:

```javascript
// eslint.config.mjs
import prettier from 'eslint-config-prettier'

export default [
  // ... other configs
  prettier,
]
```

### Tailwind Class Sorting

The included `prettier-plugin-tailwindcss` automatically sorts Tailwind classes in order of specificity:

```typescript
// Before
<div className="md:text-lg text-base rounded-lg bg-blue-500 p-4">

// After
<div className="rounded-lg bg-blue-500 p-4 text-base md:text-lg">
```

This ensures consistency across the codebase without manual sorting.
