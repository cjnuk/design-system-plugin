# LAYER-10: TOOLING CONFIGURATION

**File:** LAYER-10-TOOLING-CONFIG.md
**Purpose:** Centralized reference for development tooling configuration
**Audience:** Developers, DevOps, LLM agents
**Last Updated:** February 2026

---

## TABLE OF CONTENTS

1. [ESLint](#eslint)
2. [Prettier](#prettier)
3. [TypeScript](#typescript)
4. [Tailwind CSS](#tailwind-css)
5. [VS Code](#vs-code)
6. [Package Scripts](#package-scripts)

---

## ESLINT

### Flat Config (ESLint 9+)

```javascript
// eslint.config.mjs
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
        // Requires Node.js 20.11.0+ (import.meta.dirname is available in ESM modules)
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
      react: { version: 'detect' },
    },
  },
  {
    ignores: ['node_modules/', '.next/', 'dist/', 'build/', '*.config.*'],
  }
)
```

### Key Rules

| Rule | Purpose |
|------|---------|
| `react/jsx-no-leaked-render` | Prevents `{0 && <Component />}` rendering "0" |
| `jsx-a11y/prefer-tag-over-role` | Enforces semantic HTML over ARIA roles |
| `consistent-type-imports` | Ensures `import type` for type-only imports |

---

## PRETTIER

### Configuration

```json
// .prettierrc
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "semi": false,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

### Ignore File

```gitignore
# .prettierignore
node_modules/
.next/
dist/
build/
pnpm-lock.yaml
package-lock.json
```

---

## TYPESCRIPT

### Strict Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    // Strict type checking
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true,

    // Module resolution
    "moduleResolution": "bundler",
    "module": "ESNext",
    "target": "ES2022",
    "lib": ["DOM", "DOM.Iterable", "ES2022"],

    // Interop
    "esModuleInterop": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "isolatedModules": true,

    // React
    "jsx": "preserve",

    // Performance
    "incremental": true,

    // Path aliases
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src", "*.config.ts"],
  "exclude": ["node_modules"]
}
```

### Key Strict Settings

| Setting | What It Does |
|---------|--------------|
| `noUncheckedIndexedAccess` | `arr[0]` returns `T \| undefined` instead of `T` |
| `exactOptionalPropertyTypes` | Distinguishes `{ a?: string }` from `{ a: string \| undefined }` |
| `noImplicitReturns` | Requires return in all code paths |

---

## TAILWIND CSS

### Base Configuration

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config: Config = {
  darkMode: ['class'],
  content: [
    './src/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
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
        secondary: {
          DEFAULT: 'hsl(var(--secondary))',
          foreground: 'hsl(var(--secondary-foreground))',
        },
        destructive: {
          DEFAULT: 'hsl(var(--destructive))',
          foreground: 'hsl(var(--destructive-foreground))',
        },
        muted: {
          DEFAULT: 'hsl(var(--muted))',
          foreground: 'hsl(var(--muted-foreground))',
        },
        accent: {
          DEFAULT: 'hsl(var(--accent))',
          foreground: 'hsl(var(--accent-foreground))',
        },
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
}

export default config
```

---

## VS CODE

### Workspace Settings

```json
// .vscode/settings.json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "non-relative",
  "typescript.tsdk": "node_modules/typescript/lib",
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"],
    ["cx\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ]
}
```

### Recommended Extensions

```json
// .vscode/extensions.json
{
  "recommendations": [
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "bradlc.vscode-tailwindcss",
    "formulahendry.auto-rename-tag",
    "ms-vscode.vscode-typescript-next"
  ]
}
```

---

## PACKAGE SCRIPTS

```json
// package.json scripts
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage"
  }
}
```

---

## DEPENDENCIES

### Core

```json
{
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "next": "^15.0.0"
  }
}
```

### Development

```json
{
  "devDependencies": {
    "typescript": "^5.6.0",
    "eslint": "^9.15.0",
    "@eslint/js": "^9.0.0",
    "typescript-eslint": "^7.0.0",
    "eslint-plugin-react": "^7.33.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-jsx-a11y": "^6.8.0",
    "prettier": "^3.2.0",
    "prettier-plugin-tailwindcss": "^0.5.0",
    "tailwindcss": "^3.4.0",
    "tailwindcss-animate": "^1.0.7",
    "@types/react": "^18.2.0",
    "@types/node": "^20.0.0"
  }
}
```

---

**LLM usage rule:** When setting up a new project, use this layer as the source of truth for configuration files. Copy configurations verbatim unless project-specific requirements dictate otherwise.

---

**Related:** [LAYER-01-DESIGN-TOKENS](./LAYER-01-DESIGN-TOKENS.md) | `/ds-lint-setup` skill
