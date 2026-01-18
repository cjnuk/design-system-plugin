---
title: "Appendix E: Migration Paths"
version: "2.1"
audience: ["architects", "tech-leads", "llm-agents"]
dependencies: ["LAYER-00-STRATEGIC-DECISIONS"]
last_updated: "2026-01-18"
---

# APPENDIX E: MIGRATION PATHS

**Purpose:** Strategies for migrating to different tools while preserving design system foundations.

---

## ARCHITECTURE REVIEW

The design system is intentionally layered:

```
┌────────────────────────────────────────────┐
│ FOUNDATIONS (Layers 0-8)                   │
│ Technology-Independent                      │
│ • Tokens, patterns, accessibility          │
│ • NEVER changes during migration           │
└────────────────────────────────────────────┘
                    ↓
┌────────────────────────────────────────────┐
│ IMPLEMENTATION (Layers 9-12)               │
│ Technology-Specific                         │
│ • Tailwind + shadcn/ui (current)           │
│ • ONLY this changes during migration       │
└────────────────────────────────────────────┘
```

**Key Insight:** Migrations only affect implementation layers. Design decisions survive.

---

## MIGRATION 1: TAILWIND CSS → CSS MODULES

### When to Consider
- Team prefers scoped CSS over utility classes
- Large-scale app with CSS organization challenges
- Moving to a CSS-in-JS-free architecture

### Impact Assessment

| Layer | Impact | Effort |
|-------|--------|--------|
| 0-3 | None | 0 |
| 4 | **HIGH** | 2-3 weeks |
| 5-8 | None | 0 |
| 9-12 | **MEDIUM** | 1-2 weeks |

### Migration Steps

1. **Create CSS module files:**
```css
/* Button.module.css */
.button {
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  font-weight: 500;
}

.primary {
  background-color: var(--color-primary-500);
  color: white;
}

.secondary {
  background-color: var(--color-slate-200);
  color: var(--color-slate-900);
}
```

2. **Convert components:**
```jsx
// Before (Tailwind)
<button className="px-4 py-2 bg-blue-500 text-white rounded">

// After (CSS Modules)
import styles from './Button.module.css'
<button className={`${styles.button} ${styles.primary}`}>
```

3. **Preserve tokens via CSS variables:**
```css
:root {
  --color-primary-500: #3b82f6;
  --spacing-4: 1rem;
  /* All tokens from LAYER-01 */
}
```

### What Stays the Same
- Token values (LAYER-01)
- Component APIs and props (LAYER-02)
- Layout patterns (LAYER-03)
- Responsive breakpoints (LAYER-05)
- State patterns (LAYER-06)
- Interaction patterns (LAYER-07)
- Accessibility requirements (LAYER-08)

---

## MIGRATION 2: SHADCN/UI → CHAKRA UI

### When to Consider
- Need built-in theming system
- Team prefers Chakra's API
- Want more opinionated component behavior

### Impact Assessment

| Layer | Impact | Effort |
|-------|--------|--------|
| 0-3 | None | 0 |
| 4 | **MEDIUM** | 1 week |
| 5-8 | None | 0 |
| 9b-12b | **HIGH** | 3-4 weeks |

### Migration Steps

1. **Map tokens to Chakra theme:**
```jsx
// chakra-theme.ts
import { extendTheme } from '@chakra-ui/react'

export const theme = extendTheme({
  colors: {
    primary: {
      50: '#eff6ff',
      500: '#3b82f6',
      // ... from LAYER-01
    }
  },
  space: {
    1: '4px',
    2: '8px',
    // ... from LAYER-01
  }
})
```

2. **Convert components:**
```jsx
// Before (shadcn/ui)
import { Button } from '@/components/ui/button'
<Button variant="outline" size="lg">

// After (Chakra)
import { Button } from '@chakra-ui/react'
<Button variant="outline" size="lg">
```

3. **Update form handling:**
```jsx
// Chakra uses different form component APIs
// React Hook Form integration similar but wrapped differently
```

### What Stays the Same
- Token values map 1:1
- Component intent (what each component does)
- Accessibility requirements
- State management patterns
- All non-component Layers

---

## MIGRATION 3: REACT → VUE

### When to Consider
- Team expertise shift
- Project requirements change
- Organization standardization

### Impact Assessment

| Layer | Impact | Effort |
|-------|--------|--------|
| 0-3 | None | 0 |
| 4 | **LOW** | 1 week |
| 5 | None | 0 |
| 6 | **HIGH** | 2-3 weeks |
| 7-8 | **LOW** | 1 week |
| 9-12 | **HIGH** | 4-6 weeks |

### Migration Steps

1. **Tailwind works identically** — No change needed

2. **Convert components to Vue SFCs:**
```vue
<!-- Button.vue -->
<template>
  <button :class="classes" @click="$emit('click')">
    <slot />
  </button>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  variant: { type: String, default: 'primary' },
  size: { type: String, default: 'md' }
})

const classes = computed(() => ({
  'px-4 py-2 rounded': true,
  'bg-blue-500 text-white': props.variant === 'primary',
  // ...
}))
</script>
```

3. **Migrate state management:**
```javascript
// React (Zustand) → Vue (Pinia)
// store/user.js
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null,
    isLoading: false
  }),
  actions: {
    async login(email, password) {
      // Same logic as Zustand
    }
  }
})
```

### What Stays the Same
- All token definitions
- CSS/Tailwind classes
- Component visual design
- Accessibility patterns
- Responsive breakpoints

---

## MIGRATION 4: TAILWIND → TAILWIND V4

### When to Consider
- Taking advantage of new Tailwind v4 features
- Performance improvements
- New CSS features (container queries, cascade layers)

### Impact Assessment

| Layer | Impact | Effort |
|-------|--------|--------|
| 0-3 | None | 0 |
| 4 | **MEDIUM** | 1-2 days |
| 5-8 | None | 0 |
| 9-12 | **LOW** | 1-2 days |

### Migration Steps

1. **Update config format:**
```css
/* tailwind.config.ts → app.css */
@import "tailwindcss";

@theme {
  --color-primary-500: #3b82f6;
  --spacing-4: 1rem;
  /* Tokens now in CSS */
}
```

2. **Update any deprecated utilities:**
```jsx
// Check Tailwind v4 migration guide for specifics
// Most utilities unchanged
```

3. **Leverage new features:**
```jsx
// Container queries (now native)
<div className="@container">
  <div className="@md:flex-row">
```

---

## MIGRATION 5: ADDING SERVER COMPONENTS (REACT 19)

### When to Consider
- Moving to Next.js App Router
- Performance optimization
- Reducing client bundle

### Impact Assessment

| Layer | Impact | Effort |
|-------|--------|--------|
| 0-5 | None | 0 |
| 6 | **MEDIUM** | 1-2 weeks |
| 7-8 | **LOW** | 2-3 days |
| 9-12 | **MEDIUM** | 1-2 weeks |

### Migration Steps

1. **Identify server vs client components:**
```jsx
// Server Component (default in app/)
// No "use client" directive
export default async function Page() {
  const data = await fetch('/api/data')
  return <div>{data}</div>
}

// Client Component
'use client'
export function Counter() {
  const [count, setCount] = useState(0)
  // Interactive components need "use client"
}
```

2. **Split components:**
```jsx
// Before: One component with data + interaction
// After: Server component fetches, client component interacts

// ServerWrapper.tsx (no directive)
export default async function ServerWrapper() {
  const data = await getData()
  return <ClientComponent data={data} />
}

// ClientComponent.tsx
'use client'
export function ClientComponent({ data }) {
  // All interactive logic here
}
```

---

## GENERAL MIGRATION STRATEGY

### Phase 1: Preparation (Week 1)
- [ ] Audit current usage of each Layer
- [ ] Document custom modifications
- [ ] Set up parallel development environment
- [ ] Create comprehensive test suite

### Phase 2: Foundation (Week 2)
- [ ] Migrate tokens (LAYER-01) to new format
- [ ] Set up new styling system
- [ ] Verify tokens work correctly

### Phase 3: Components (Weeks 3-4)
- [ ] Migrate one component at a time
- [ ] Start with simple (Button, Input)
- [ ] Progress to complex (DataTable, Dialog)
- [ ] Test each component thoroughly

### Phase 4: Patterns (Week 5)
- [ ] Verify state patterns work
- [ ] Test interaction patterns
- [ ] Accessibility audit
- [ ] Performance testing

### Phase 5: Validation (Week 6)
- [ ] Full regression testing
- [ ] User acceptance testing
- [ ] Documentation update
- [ ] Gradual rollout

---

## RISK MITIGATION

### High-Risk Items
- Component API changes
- State management differences
- Build system configuration
- Third-party integrations

### Mitigation Strategies
1. **Feature flags** — Roll out gradually
2. **Parallel systems** — Run old and new simultaneously
3. **Comprehensive tests** — Catch regressions early
4. **Documentation** — Keep team aligned
5. **Rollback plan** — Always have an exit strategy

---

## WHEN NOT TO MIGRATE

**Don't migrate if:**
- Current system is working well
- Team is productive
- No clear benefit
- Tight deadline approaching
- Insufficient testing resources

**The best migration is often no migration.**

---

## LLM MIGRATION ASSISTANCE

When asking an LLM to help with migration:

1. **Specify source and target:**
   "Migrate this Tailwind component to CSS Modules"

2. **Provide token context:**
   "Using design tokens from LAYER-01"

3. **Preserve patterns:**
   "Keep the same component API and props"

4. **Request tests:**
   "Include unit tests for the migrated component"

---

**Related:** [LAYER-00-STRATEGIC-DECISIONS](../LAYER-00-STRATEGIC-DECISIONS.md) | [LAYER-04-CSS-ARCHITECTURE](../LAYER-04-CSS-ARCHITECTURE.md)
