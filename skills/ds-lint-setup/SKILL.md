---
skill: ds-lint-setup
description: Configure ESLint and Prettier with Vercel-style best practices for TypeScript/React projects
triggers:
  - "set up linting"
  - "configure eslint"
  - "lint setup"
  - "ds lint"
  - "add prettier"
---

# Linting & Formatting Setup

Configure ESLint (flat config) and Prettier with Vercel-style best practices for TypeScript/React projects.

## Context Resolution

Before executing, check for project-specific configuration:

1. Read `.claude/design-system/project-config.yaml` for project metadata
2. Read `.claude/design-system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md` for project patterns
3. Fall back to `${CLAUDE_PLUGIN_ROOT}/system/` layer files for defaults

## What This Does

1. Installs ESLint 9+ with flat config
2. Configures TypeScript strict type checking
3. Sets up React and JSX accessibility rules
4. Configures Prettier with Tailwind plugin
5. Adds VS Code integration settings

## Prerequisites

- Node.js 18+
- TypeScript project
- Package manager (npm/pnpm/yarn/bun)

## Setup Steps

### 1. Install Dependencies

```bash
npm install -D eslint @eslint/js typescript-eslint eslint-plugin-react eslint-plugin-react-hooks eslint-plugin-jsx-a11y
npm install -D prettier prettier-plugin-tailwindcss
```

### 2. Create ESLint Config

Create `eslint.config.mjs`:

```javascript
import js from '@eslint/js'
import tseslint from 'typescript-eslint'
import react from 'eslint-plugin-react'
import reactHooks from 'eslint-plugin-react-hooks'
import jsxA11y from 'eslint-plugin-jsx-a11y'

export default tseslint.config(
  js.configs.recommended,
  ...tseslint.configs.strictTypeChecked,
  ...tseslint.configs.stylisticTypeChecked,
  {
    languageOptions: {
      parserOptions: {
        project: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },
  },
  {
    plugins: {
      react,
      'react-hooks': reactHooks,
      'jsx-a11y': jsxA11y,
    },
    rules: {
      // React
      ...react.configs.recommended.rules,
      ...reactHooks.configs.recommended.rules,
      'react/react-in-jsx-scope': 'off',
      'react/prop-types': 'off',
      'react/jsx-no-leaked-render': 'error',

      // Accessibility
      ...jsxA11y.configs.recommended.rules,
      'jsx-a11y/prefer-tag-over-role': 'error',

      // TypeScript
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      '@typescript-eslint/consistent-type-imports': 'error',
      '@typescript-eslint/no-non-null-assertion': 'error',
    },
    settings: {
      react: {
        version: 'detect',
      },
    },
  },
  {
    ignores: ['node_modules/', '.next/', 'dist/', 'build/'],
  }
)
```

### 3. Create Prettier Config

Create `.prettierrc`:

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

### 4. Create TypeScript Strict Config

Update `tsconfig.json` to include strict settings:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

### 5. Add VS Code Settings

Create `.vscode/settings.json`:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "non-relative"
}
```

### 6. Add Package Scripts

Add to `package.json`:

```json
{
  "scripts": {
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  }
}
```

## Validation Checklist

After setup, verify:

- [ ] `npm run lint` runs without configuration errors
- [ ] `npm run format` formats files correctly
- [ ] VS Code shows ESLint errors inline
- [ ] Prettier formats on save
- [ ] TypeScript strict errors appear where expected

## Key Rules Explained

| Rule | Purpose |
|------|---------|
| `noUncheckedIndexedAccess` | Forces explicit undefined checks for array/object access |
| `react/jsx-no-leaked-render` | Prevents `{count && <Component />}` bugs with falsy 0 |
| `jsx-a11y/prefer-tag-over-role` | Prefer `<button>` over `<div role="button">` |
| `consistent-type-imports` | Use `import type` for type-only imports |

## Next Steps

- Run `npm run lint:fix` to auto-fix existing issues
- Use `/ds-review` to validate component code
- See [LAYER-08-ACCESSIBILITY](../../system/LAYER-08-ACCESSIBILITY.md) for a11y guidelines
