# PRIME: Design System Plugin

**Version:** 3.0.0
**Last Updated:** 2026-02-05
**Purpose:** Bootstrap document for AI agents and developers

---

## 1. PROJECT IDENTITY

| Attribute | Value |
|-----------|-------|
| **Name** | Design System Plugin |
| **Version** | 3.0.0 |
| **Purpose** | AI-powered design system for building UI components with shadcn/ui, semantic tokens, and WCAG accessibility |
| **Deployment** | Claude Code Plugin (marketplace install or git clone) |
| **Primary Users** | Developers building React/TypeScript applications with Tailwind CSS |
| **Repository** | https://github.com/cjnuk/design-system-plugin |
| **License** | MIT |

### 1.1 Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | React 19 + TypeScript |
| Styling | Tailwind CSS v4 |
| Components | shadcn/ui + Radix UI |
| Variants | CVA (class-variance-authority) |
| Forms | react-hook-form + Zod |
| Tables | TanStack Table |
| Accessibility | WCAG 2.1 AA |

---

## 2. CORE DIFFERENTIATORS (INVARIANTS)

These principles are **non-negotiable**. Violations indicate incorrect implementation.

| # | Invariant | Rationale |
|---|-----------|-----------|
| 1 | **Semantic tokens only** | No hardcoded colors (`bg-slate-900`). Use `bg-primary`, `text-muted-foreground`. |
| 2 | **CVA for all variants** | Never inline conditional classes. Use `cva()` for type-safe variants. |
| 3 | **TypeScript + VariantProps** | Export `Props` interface extending `VariantProps<typeof variants>`. No `any`. |
| 4 | **WCAG AA accessibility** | 4.5:1 contrast, keyboard nav, aria-labels on icon buttons, 44px touch targets. |
| 5 | **Extend shadcn/ui** | Never rebuild components. Import from `@/components/ui/`, not packages. |
| 6 | **Mobile-first responsive** | Base styles for mobile, breakpoint modifiers (`md:`, `lg:`) for larger. |
| 7 | **Reduced motion respect** | All animations must honor `prefers-reduced-motion`. |
| 8 | **On-demand knowledge loading** | Agents load LAYER files as needed. Never embed all knowledge upfront. |
| 9 | **Domain separation** | APPLICATION-DOMAIN vs MARKETING-DOMAIN patterns never mix. |
| 10 | **PostToolUse validation** | All agent output validated by hooks before returning to user. |

---

## 3. ARCHITECTURE

### 3.1 Plugin Structure

```
/Users/chrisnorris/projects/design-system-plugin/
├── plugin.json                 # Plugin metadata (v3.0.0)
├── PRIME.md                    # This document
├── README.md                   # User documentation
├── GETTING-STARTED.md          # Quick start guide
├── ARCHITECTURE-V3.md          # Detailed architecture
│
├── skills/                     # 16 skill directories
│   └── ds-*/SKILL.md           # Skill definitions
├── agents/                     # 8 execution agents
│   └── *-agent.md              # Agent logic
├── knowledge/                  # Design system docs
│   ├── LAYER-*.md              # Core layers (00-10)
│   ├── PROMPTS/                # Task templates
│   ├── APPENDICES/             # Reference guides
│   ├── APPLICATION-DOMAIN/     # SPA patterns
│   └── MARKETING-DOMAIN/       # Marketing patterns
├── hooks/                      # Validation hooks
│   └── validate-output.md      # PostToolUse hook
├── templates/                  # Project scaffolding
└── configs/                    # ESLint, Prettier, TS configs
```

### 3.2 Knowledge Layer Architecture

```
LAYER-00  Strategic Decisions   ─── WHY we chose this stack
LAYER-01  Design Tokens         ─── Colors, spacing, typography
LAYER-02  Components            ─── Component specs and APIs
LAYER-03  Layout Patterns       ─── Grid, flexbox, containers
LAYER-04  CSS Architecture      ─── Tailwind organization
LAYER-05  Responsive Design     ─── Breakpoints, mobile-first
LAYER-06  State Management      ─── Jotai, server components
LAYER-07  Interaction Patterns  ─── Loading, error, success
LAYER-07b Prompt Patterns       ─── AI/chat UI patterns
LAYER-07c Motion Patterns       ─── Animations, transitions
LAYER-08  Accessibility         ─── WCAG AA compliance
LAYER-09  CI Automation         ─── Testing, CI/CD
LAYER-10  Tooling Config        ─── Build tools, linting
```

---

## 4. KEY ACTORS/MODULES

### 4.1 Skills (16 Total)

| Skill | Purpose |
|-------|---------|
| `ds-init` | Initialize design system in new project |
| `ds-component` | Generate component with CVA, types, a11y |
| `ds-form-field` | Add form field with Zod validation |
| `ds-accessibility` | Fix WCAG 2.1 AA violations |
| `ds-dark-mode` | Implement dark mode theming |
| `ds-tokens` | Design token reference |
| `ds-review` | Review code against design system |
| `ds-audit` | Audit codebase for compliance |
| `ds-migrate` | Migrate from MUI/Chakra/Bootstrap |
| `ds-compound-component` | Create compound component pattern |
| `ds-data-table` | TanStack Table implementation |
| `ds-virtualize` | Virtual scrolling for large lists |
| `ds-animation` | Motion patterns with reduced-motion |
| `ds-lint-setup` | Configure ESLint/Prettier |
| `ds-repair` | Fix corrupted config files |
| `ds-setup` | Legacy setup (use ds-init) |

### 4.2 Agents (8 Total)

| Agent | Purpose |
|-------|---------|
| `router-agent` | Route requests to specialized agents |
| `component-agent` | Component generation |
| `form-agent` | Form field creation |
| `accessibility-agent` | WCAG compliance fixes |
| `theming-agent` | Dark mode and tokens |
| `review-agent` | Code review |
| `setup-agent` | Project initialization |
| `migration-agent` | Framework migration |

---

## 5. CORE WORKFLOWS

### 5.1 Plugin Installation (One-time)

```bash
# Option A: Marketplace install
claude plugin install design-system-plugin@cjnuk-marketplace

# Option B: Git clone
git clone https://github.com/cjnuk/design-system-plugin ~/.claude/plugins/design-system
```

### 5.2 Project Initialization (Per-project)

```
/ds-init
```

Creates:
```
.claude/design-system/
├── project-config.yaml
└── APPLICATION-DOMAIN/
    ├── 09b-COMPONENTS.md
    ├── 10b-SETUP.md
    ├── 11b-TESTING.md
    └── 12b-IMPLEMENTATION.md
```

### 5.3 Component Creation

```
/ds-component Button
```

Generates component with:
- CVA variants
- TypeScript Props interface
- WCAG accessibility
- Semantic tokens

### 5.4 Code Review

```
/ds-review
```

Checks:
- Semantic token usage
- Component patterns
- Accessibility compliance
- TypeScript types

---

## 6. KEY FILES

### 6.1 Configuration

| File | Purpose |
|------|---------|
| `/Users/chrisnorris/projects/design-system-plugin/plugin.json` | Plugin manifest |
| `/Users/chrisnorris/projects/design-system-plugin/ARCHITECTURE-V3.md` | Architecture spec |

### 6.2 Documentation

| File | Purpose |
|------|---------|
| `/Users/chrisnorris/projects/design-system-plugin/README.md` | Main docs |
| `/Users/chrisnorris/projects/design-system-plugin/GETTING-STARTED.md` | Quick start |
| `/Users/chrisnorris/projects/design-system-plugin/QUICK-REFERENCE.md` | Token cheatsheet |

### 6.3 Core Knowledge

| File | Purpose |
|------|---------|
| `/Users/chrisnorris/projects/design-system-plugin/knowledge/00-MASTER.md` | Entry point |
| `/Users/chrisnorris/projects/design-system-plugin/knowledge/LLM-AGENT-GUIDE.md` | Agent routing |
| `/Users/chrisnorris/projects/design-system-plugin/knowledge/LAYER-01-DESIGN-TOKENS.md` | Token specs |
| `/Users/chrisnorris/projects/design-system-plugin/knowledge/LAYER-02-COMPONENTS.md` | Component specs |
| `/Users/chrisnorris/projects/design-system-plugin/knowledge/LAYER-08-ACCESSIBILITY.md` | WCAG guide |

---

## 7. KEY COMMANDS

### 7.1 Skill Commands

```
/ds-init                    # Initialize project
/ds-component [Name]        # Create component
/ds-form-field [name]       # Create form field
/ds-review                  # Review code
/ds-audit                   # Audit codebase
/ds-tokens                  # Token reference
/ds-dark-mode               # Add dark mode
/ds-accessibility           # Fix a11y issues
/ds-migrate                 # Migrate framework
/ds-repair                  # Fix config
```

### 7.2 Installation

```bash
# Marketplace (recommended)
claude plugin install design-system-plugin@cjnuk-marketplace

# Git clone
git clone https://github.com/cjnuk/design-system-plugin ~/.claude/plugins/design-system

# Update
cd ~/.claude/plugins/design-system && git pull
```

---

## 8. ANTI-PATTERNS

| Anti-Pattern | Correct Approach |
|--------------|------------------|
| `bg-slate-900` (hardcoded) | `bg-primary` (semantic token) |
| `className="text-[#1a1a1a]"` | Use token from design system |
| Custom Button from scratch | Extend `@/components/ui/button` |
| `<input placeholder="Email">` only | Add visible `<Label>` component |
| Color as only indicator | Add icon, text, or pattern |
| `any` type | Proper TypeScript types |
| Desktop-first CSS | Mobile-first with breakpoint modifiers |
| Icon button without aria-label | Always add `aria-label` |
| Animation without reduced-motion | Check `prefers-reduced-motion` |
| Touch target < 44px | Minimum 44x44 pixels |

---

## 9. TECHNICAL INSIGHTS

### 9.1 Gotchas

| Issue | Solution |
|-------|----------|
| Radix UI composition | Must follow strict parent-child hierarchy |
| Tailwind purging | Class names must be complete strings (no interpolation) |
| Hydration mismatch | Wrap client-only code in useEffect or dynamic import |
| cva variant types | Use `VariantProps<typeof variants>` |
| Form validation | Use react-hook-form + zod |
| Dark mode flash | Use `next-themes` with `attribute="class"` |

### 9.2 Context Efficiency

| Metric | Value |
|--------|-------|
| Main context (skill trigger) | ~25 lines |
| Agent context (with knowledge) | ~600-2500 lines |
| Context reduction | ~90% |

---

## 10. ON STARTUP

### 10.1 Read Order

1. `PRIME.md` (this document)
2. `knowledge/LLM-AGENT-GUIDE.md` for task routing
3. Task-specific LAYER files as needed

### 10.2 Task-Specific Loading

| Task | Load |
|------|------|
| Create component | LAYER-02, LAYER-08, PROMPTS/01-NEW-COMPONENT |
| Form field | LAYER-02, PROMPTS/02-FORM-FIELD |
| Accessibility fix | LAYER-08, PROMPTS/03-FIX-ACCESSIBILITY |
| Dark mode | LAYER-01, PROMPTS/04-DARK-MODE |
| Code review | LAYER-01, LAYER-08, PROMPTS/05-CODE-REVIEW |
| Migration | LAYER-01, LAYER-02, APPENDIX-E |

---

## Quick Reference Card

```
INSTALL:     claude plugin install design-system-plugin@cjnuk-marketplace
INIT:        /ds-init
COMPONENT:   /ds-component [Name]
FORM:        /ds-form-field [name]
REVIEW:      /ds-review

TOKENS:      Always semantic (bg-primary, not bg-slate-900)
COMPONENTS:  Extend shadcn/ui, never rebuild
TYPES:       No any, export Props + VariantProps
A11Y:        WCAG AA, keyboard nav, aria-labels
RESPONSIVE:  Mobile-first, breakpoint modifiers

REPO:        https://github.com/cjnuk/design-system-plugin
VERSION:     3.0.0
```

---

**Document Version:** 3.0.0
**Generated:** 2026-02-05
