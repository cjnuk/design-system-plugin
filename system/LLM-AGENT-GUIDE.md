---
title: "LLM Agent Guide"
version: "2.1"
audience: ["llm-agents", "developers"]
dependencies: ["00-MASTER"]
last_updated: "2026-01-18"
---

# LLM AGENT GUIDE

**Purpose:** Retrieval order, canonical files, task-specific paths, and output rules for AI agents implementing this design system.

---

## QUICK START

For any UI implementation task, retrieve files in this order:

1. **This guide** — task routing and retrieval paths
2. **00-MASTER.md** — system overview and navigation
3. **Task-specific files** — see retrieval paths below

---

## TASK-SPECIFIC RETRIEVAL PATHS

### Create New Component

```
PROMPTS/01-NEW-COMPONENT.md
LAYER-01-DESIGN-TOKENS.md
LAYER-02-COMPONENTS.md
LAYER-08-ACCESSIBILITY.md
APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md
```

### Add Form Field

```
PROMPTS/02-FORM-FIELD.md
LAYER-02-COMPONENTS.md
APPLICATION-DOMAIN/12b-IMPLEMENTATION.md
```

### Fix Accessibility Issue

```
PROMPTS/03-FIX-ACCESSIBILITY.md
LAYER-08-ACCESSIBILITY.md
APPENDICES/APPENDIX-D-TROUBLESHOOTING.md
```

### Implement Dark Mode / Theming

```
PROMPTS/04-DARK-MODE.md
LAYER-01-DESIGN-TOKENS.md
APPLICATION-DOMAIN/10b-SETUP.md
```

### Code Review

```
PROMPTS/05-CODE-REVIEW.md
00-MASTER.md
LAYER-01-DESIGN-TOKENS.md
LAYER-08-ACCESSIBILITY.md
```

### Migrate from Other Framework

```
PROMPTS/06-MIGRATION.md
APPENDICES/APPENDIX-E-MIGRATION-PATHS.md
LAYER-01-DESIGN-TOKENS.md
LAYER-02-COMPONENTS.md
```

### Setup New Project

```
LAYER-00-STRATEGIC-DECISIONS.md
MARKETING-DOMAIN/10a-SETUP.md (marketing site)
APPLICATION-DOMAIN/10b-SETUP.md (app)
```

### Add Tests

```
APPLICATION-DOMAIN/11b-TESTING.md
LAYER-08-ACCESSIBILITY.md (for jest-axe)
```

---

## CANONICAL FILE MAP

### Core Layers

| Layer | Canonical File | Purpose |
|-------|----------------|----------|
| 00 | `LAYER-00-STRATEGIC-DECISIONS.md` | Tool choices, constraints |
| 01 | `LAYER-01-DESIGN-TOKENS.md` | Design tokens (colors, spacing) |
| 02 | `LAYER-02-COMPONENTS.md` | Component specs and APIs |
| 03 | `LAYER-03-LAYOUT-PATTERNS.md` | Layout systems |
| 04 | `LAYER-04-CSS-ARCHITECTURE.md` | Tailwind organization |
| 05 | `LAYER-05-RESPONSIVE-DESIGN.md` | Breakpoints, mobile-first |
| 06 | `LAYER-06-STATE-MANAGEMENT.md` | State patterns (Jotai) |
| 07 | `LAYER-07-INTERACTION-PATTERNS.md` | Loading, error, success |
| 07b | `LAYER-07b-PROMPT-PATTERNS.md` | AI prompt UI patterns |
| 08 | `LAYER-08-ACCESSIBILITY.md` | WCAG 2.1 AA compliance |
| 09 | `LAYER-09-CI-AUTOMATION.md` | CI/CD, Storybook, TypeDoc |

### Domain Guides

| Domain | Guide | Subdirectory Files |
|--------|-------|--------------------|
| Marketing | `MARKETING-DOMAIN-guide.md` | `09a-TEMPLATES`, `10a-SETUP`, `12a-IMPLEMENTATION` |
| Application | `APPLICATION-DOMAIN-guide.md` | `09b-COMPONENTS`, `10b-SETUP`, `11b-TESTING`, `12b-IMPLEMENTATION` |

### Appendices

| ID | File | Content |
|----|------|----------|
| A | `APPENDICES/APPENDIX-A-TAILWIND-UTILITIES.md` | Utility class reference |
| B | `APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md` | ADRs for component choices |
| C | `APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md` | Radix primitive mappings |
| D | `APPENDICES/APPENDIX-D-TROUBLESHOOTING.md` | Common issues and fixes |
| E | `APPENDICES/APPENDIX-E-MIGRATION-PATHS.md` | Migration from other libraries |

### Prompts

| Prompt | Use Case |
|--------|----------|
| `PROMPTS/01-NEW-COMPONENT.md` | Creating components |
| `PROMPTS/02-FORM-FIELD.md` | Adding form fields |
| `PROMPTS/03-FIX-ACCESSIBILITY.md` | Fixing a11y issues |
| `PROMPTS/04-DARK-MODE.md` | Theming implementation |
| `PROMPTS/05-CODE-REVIEW.md` | Code review checklist |
| `PROMPTS/06-MIGRATION.md` | Framework migration |

---

## OUTPUT RULES

### Token Usage (MANDATORY)

```typescript
// ❌ NEVER use hardcoded colors
className="bg-slate-900 text-white border-gray-200"

// ✅ ALWAYS use semantic tokens
className="bg-primary text-primary-foreground border-border"
```

### Component Patterns (MANDATORY)

```typescript
// ❌ NEVER create custom implementations when shadcn/ui exists
const CustomButton = styled.button`...`

// ✅ ALWAYS use/extend shadcn/ui components
import { Button } from '@/components/ui/button'
```

### Variant Definition (MANDATORY)

```typescript
// ❌ NEVER use inline conditional classes
className={`btn ${isPrimary ? 'btn-primary' : 'btn-secondary'}`}

// ✅ ALWAYS use cva for variants
const buttonVariants = cva("base-classes", {
  variants: { variant: { primary: "...", secondary: "..." } }
})
```

### Accessibility (MANDATORY)

- All interactive elements must be keyboard accessible
- All images need `alt` text
- All form inputs need associated labels
- Icon-only buttons need `aria-label`
- Color contrast must meet WCAG 2.1 AA (4.5:1 text, 3:1 UI)
- Respect `prefers-reduced-motion`

### TypeScript (MANDATORY)

- Export prop interfaces for all components
- Use `VariantProps<typeof componentVariants>` for variant types
- No `any` types

---

## RETRIEVAL RULES

1. **Task-first:** Always check task-specific retrieval paths before general files
2. **Prompts library:** Use `PROMPTS/` templates for structured output
3. **Semantic tokens:** Never output hardcoded colors; use token names
4. **Accessibility:** Always include a11y considerations in output
5. **Reduced motion:** Respect user preference when outputting animations
6. **TypeScript:** Always include proper types in code output

---

## GENERATED DOCS

If available, check for auto-generated documentation:
- `docs/components/index.html` — TypeDoc component API
- `docs/storybook/` — Storybook stories
- `docs/components.json` — Component metadata

Fall back to `LAYER-02-COMPONENTS.md` if generated docs are unavailable.

---

**See also:** [00-MASTER](./00-MASTER.md) | [_FILE-MANIFEST](./_FILE-MANIFEST.md)
