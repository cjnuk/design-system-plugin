# Framework-Specific Setup

Project-specific setup and configuration for your chosen framework.

## Next.js Setup

### Directory Structure

```
src/
  app/                          # Next.js app router
  components/
    ui/                         # shadcn/ui components
    layout/                     # Layout components
  lib/
    utils.ts                    # cn() utility
    constants.ts
  styles/
    globals.css                 # Tailwind imports
```

### Key Configuration Files

**tsconfig.json**:
- `strict: true`
- `noUncheckedIndexedAccess: true`
- `exactOptionalPropertyTypes: true`

**tailwind.config.ts**:
- Theme extends with semantic tokens (LAYER-01)
- Plugins configured

**next.config.ts**:
- TypeScript strict mode
- SWC minification enabled

### Aliases

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Environment Variables

Create `.env.local`:
```
NEXT_PUBLIC_API_URL=http://localhost:3000
```

## Remix Setup

### Directory Structure

```
app/
  routes/
  components/
    ui/                         # shadcn/ui components
  styles/
```

### Configuration

**tailwind.config.ts**:
- Configured with remix preset

## Vite Setup

### Directory Structure

```
src/
  components/
    ui/                         # shadcn/ui components
  pages/
  styles/
```

### Vite Config

```typescript
export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```

---

## Shared Configuration

### Tailwind Configuration

All frameworks use the same Tailwind config structure:

1. **Semantic tokens** from LAYER-01
2. **Dark mode** support (class-based)
3. **Plugins**: Tailwind UI, prettier-plugin-tailwindcss

### TypeScript Strict Mode

Enforced across all frameworks with strict settings.

### Linting

All projects use ESLint 9 with flat config (see /ds-lint-setup).
