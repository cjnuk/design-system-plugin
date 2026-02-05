# Quick Reference: Design System Plugin v2.2.0

## Installation

```bash
# Install globally (one-time)
git clone https://github.com/your-org/design-system-plugin ~/.claude/plugins/design-system
```

## Getting Started

### New Project

```
Step 1: "ds init"
Step 2: Answer questions about framework, testing, stack
Step 3: Claude creates .claude/design-system/ with templates
Step 4: Start using design system skills!
```

### Existing Project

```
Step 1: "ds init" to scaffold .claude/design-system/
Step 2: Or manually create and customize templates
Step 3: Commit to git so team gets configuration
```

## Available Skills

### Core

| Skill | Command | Purpose |
|-------|---------|---------|
| ds-init | "ds init" | Initialize project with design system |
| ds-component | "create component [name]" | Generate new component |
| ds-lint-setup | "set up linting" | Configure ESLint 9 + Prettier |
| ds-tokens | "show tokens" | Query design tokens |

### Patterns

| Skill | Command | Purpose |
|-------|---------|---------|
| ds-form-field | "add form field" | Form patterns with validation |
| ds-compound-component | "build compound component" | Dialog, Select, etc. |
| ds-data-table | "create data table" | TanStack Table patterns |
| ds-virtualize | "add virtual scrolling" | TanStack Virtual patterns |

### Advanced

| Skill | Command | Purpose |
|-------|---------|---------|
| ds-animation | "add animation" | Motion patterns |
| ds-dark-mode | "implement dark mode" | Dark mode support |
| ds-accessibility | "fix accessibility" | WCAG AA compliance |
| ds-review | "review code" | Validate against standards |
| ds-migrate | "migrate from MUI" | Framework migration helper |
| ds-setup | "setup project" | Legacy setup (use ds-init) |

## Project Configuration

Created in `.claude/design-system/` after `ds-init`:

```yaml
# project-config.yaml
project:
  name: "MyApp"
  type: "application"
stack:
  framework: "next"
  ui_library: "shadcn"
  testing:
    unit: "vitest"
    e2e: "playwright"
components:
  installed: [button, card, dialog]
  custom: []
```

## Project Patterns

Located in `.claude/design-system/APPLICATION-DOMAIN/`:

| File | Purpose |
|------|---------|
| `09b-COMPONENTS.md` | Component inventory |
| `10b-SETUP.md` | Framework-specific setup |
| `11b-TESTING.md` | Testing strategy |
| `12b-IMPLEMENTATION.md` | Implementation guidelines |

## Design System Principles

### 1. Semantic Tokens

```typescript
// ALWAYS use tokens
className="bg-primary text-primary-foreground"

// NEVER hardcode colors
className="bg-slate-900 text-white"
```

### 2. CVA for Variants

```typescript
const variants = cva("base", {
  variants: {
    variant: { primary: "...", secondary: "..." }
  }
})
```

### 3. TypeScript Strict

```typescript
export interface ComponentProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof componentVariants> {}
```

### 4. Accessibility (WCAG AA)

- Keyboard accessible
- `aria-label` on icon buttons
- Semantic HTML
- Proper focus indicators

## Semantic Tokens Reference

| Token | Usage |
|-------|-------|
| `bg-primary` | Primary buttons |
| `bg-secondary` | Secondary actions |
| `bg-destructive` | Danger/delete |
| `bg-muted` | Subtle backgrounds |
| `text-foreground` | Primary text |
| `text-muted-foreground` | Secondary text |
| `border-border` | Default borders |
| `ring-ring` | Focus rings |

## Architecture

### Global Plugin

- Location: `~/.claude/plugins/design-system/`
- Contains: Core knowledge, 14 skills, templates, layers
- Updateable: `git pull` for updates

### Project Config

- Location: `./<project>/.claude/design-system/`
- Contains: Project metadata, component inventory, patterns
- Versioned: Committed to git with project

### Knowledge Priority

Skills check in order:
1. Project patterns (`.claude/design-system/`)
2. Skill references (embedded in SKILL.md)
3. Core layers (system/LAYER-*.md)

## Team Collaboration

### Share Project Configuration

```bash
# In .gitignore - DO NOT ignore design-system:
!.claude/design-system/

# Team gets project patterns with project
git clone <project>
# .claude/design-system/ is checked out
```

### Update Project Patterns

As patterns emerge, update:
- `.claude/design-system/APPLICATION-DOMAIN/09b-COMPONENTS.md`
- `.claude/design-system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md`
- `.claude/design-system/project-config.yaml`

Commit changes and push to team.

## Troubleshooting

**Q: Skills not appearing?**
A: Ensure plugin installed to `~/.claude/plugins/design-system/`

**Q: How to customize for my project?**
A: Run `ds init` to create `.claude/design-system/` then edit files

**Q: Can I update the core plugin?**
A: Yes, `git pull` in plugin directory. Project files preserved automatically.

**Q: What if I need project-specific patterns?**
A: Edit `.claude/design-system/APPLICATION-DOMAIN/*.md` files

**Q: How do I share patterns with other projects?**
A: Copy and customize `.claude/design-system/APPLICATION-DOMAIN/` files

## Key Docs

- **README.md**: Full architecture documentation
- **MIGRATION-GUIDE.md**: How v2.2.0 differs from v2.1.x
- **RESTRUCTURING-SUMMARY.md**: Implementation details
- **system/LAYER-*.md**: Core design system knowledge (13 files)

## Skills Directory Structure

```
~/.claude/plugins/design-system/skills/
├── ds-accessibility/SKILL.md
├── ds-animation/SKILL.md
├── ds-component/SKILL.md
├── ds-compound-component/SKILL.md
├── ds-dark-mode/SKILL.md
├── ds-data-table/SKILL.md
├── ds-form-field/SKILL.md
├── ds-init/SKILL.md          # NEW
├── ds-lint-setup/SKILL.md
├── ds-migrate/SKILL.md
├── ds-review/SKILL.md
├── ds-setup/SKILL.md
├── ds-tokens/SKILL.md
└── ds-virtualize/SKILL.md
```

## Common Workflows

### Create Component

```
1. "create component Button"
2. Claude generates components/ui/button.tsx
3. Uses your project patterns from 12b-IMPLEMENTATION.md
4. Follows semantic tokens, CVA, accessibility
```

### Review Code

```
1. "ds review"
2. Claude checks for:
   - Hardcoded colors
   - Missing accessibility
   - Type safety issues
   - Pattern violations
```

### Setup Linting

```
1. "ds lint-setup"
2. Creates eslint.config.mjs
3. Creates .prettierrc
4. Configures VS Code settings
```

### Query Tokens

```
1. "show tokens"
2. Get semantic token reference
3. Use in components
```

## Version

- **Version**: 2.2.0
- **Architecture**: ADR-001 Hybrid
- **Release Date**: 2026-02-04
- **Status**: Production Ready

---

For detailed information, see the full README.md and system layer files.
