# Design System Plugin for Claude Code

A unified Claude Code plugin for building UI components with shadcn/ui, semantic tokens, and WCAG accessibility. Combines context-efficient agent execution with comprehensive skill documentation.

**Version:** 3.0.0

## Architecture

This plugin uses a **hybrid architecture** that provides:

- **Complete Skill Documentation**: Full SKILL.md files with detailed implementation guides
- **Agent-Centric Execution**: Specialized agents load knowledge on-demand (~90% context reduction)
- **Reference Documentation**: Advanced patterns in reference.md files for complex skills
- **Validation Hooks**: PostToolUse hooks enforce design system rules

### Context Efficiency

| Architecture | Main Context Load |
|--------------|-------------------|
| Traditional (embedded skills) | ~300 lines per command |
| Hybrid (this plugin) | ~25 lines per command |

**Result:** ~90% reduction in main session context usage while maintaining comprehensive documentation.

## Installation

### Option A: Git Clone (Recommended for Users)

```bash
git clone https://github.com/cjnuk/design-system-plugin ~/.claude/plugins/design-system
```

### Option B: Symlink (Recommended for Development)

```bash
ln -s /path/to/design-system-plugin ~/.claude/plugins/design-system
```

Changes are immediately reflected without git operations.

## Available Commands

| Command | Purpose | Agent |
|---------|---------|-------|
| `/ds-init` | Initialize project with design system | init-agent |
| `/ds-component [name]` | Generate component with cva, types, a11y | component-agent |
| `/ds-form-field [name]` | Add form field with Zod validation | form-agent |
| `/ds-accessibility` | Fix WCAG 2.1 AA violations | accessibility-agent |
| `/ds-dark-mode` | Implement dark mode theming | theming-agent |
| `/ds-tokens` | Design token reference and guidance | theming-agent |
| `/ds-review` | Review code against design system | review-agent |
| `/ds-migrate` | Migrate from MUI/Chakra/Bootstrap | migration-agent |
| `/ds-audit` | Audit codebase for compliance | audit-agent |
| `/ds-repair` | Repair corrupted config files | repair-agent |
| `/ds-data-table` | Create TanStack Table implementation | data-table-agent |
| `/ds-compound-component` | Create compound component pattern | compound-agent |
| `/ds-virtualize` | Add virtual scrolling | virtualize-agent |
| `/ds-animation` | Add motion patterns | animation-agent |
| `/ds-lint-setup` | Configure ESLint/Prettier | lint-agent |
| `/design-system` | Intelligent routing for complex requests | router-agent |

## How It Works

1. **You invoke a skill** (e.g., `/ds-component Badge`)
2. **Skill documentation is available** for reference
3. **Agent loads knowledge on-demand** via Read tool
4. **Agent executes in isolated context** - main session stays clean
5. **Hook validates output** - enforces design system rules
6. **Agent returns result** - only the output reaches your session

## Directory Structure

```
design-system-plugin/
├── plugin.json                  # v3.0.0 manifest
├── README.md                    # This file
├── GETTING-STARTED.md           # Quick start guide
├── PUBLISHING.md                # Publishing instructions
├── QUICK-REFERENCE.md           # Token/pattern cheat sheet
├── ARCHITECTURE-V3.md           # Detailed architecture
│
├── skills/                      # Complete skill documentation
│   ├── ds-init/SKILL.md         # Project initialization
│   ├── ds-component/SKILL.md    # Component generation
│   ├── ds-form-field/SKILL.md   # Form fields
│   ├── ds-accessibility/SKILL.md
│   ├── ds-dark-mode/SKILL.md
│   ├── ds-tokens/SKILL.md
│   ├── ds-review/SKILL.md
│   ├── ds-migrate/SKILL.md
│   ├── ds-audit/SKILL.md
│   ├── ds-repair/SKILL.md
│   ├── ds-data-table/
│   │   ├── SKILL.md
│   │   └── reference.md         # Advanced patterns
│   ├── ds-compound-component/
│   │   ├── SKILL.md
│   │   └── reference.md         # Advanced patterns
│   ├── ds-virtualize/SKILL.md
│   ├── ds-animation/SKILL.md
│   └── ds-lint-setup/SKILL.md
│
├── agents/                      # Specialized execution agents
│   ├── router-agent.md          # Intelligent routing
│   ├── setup-agent.md           # Project setup
│   ├── component-agent.md       # Component generation
│   ├── form-agent.md            # Form fields
│   ├── accessibility-agent.md   # A11y fixes
│   ├── theming-agent.md         # Dark mode/tokens
│   ├── review-agent.md          # Code review
│   └── migration-agent.md       # Framework migration
│
├── hooks/
│   └── validate-output.md       # PostToolUse validation
│
├── knowledge/                   # Design system documentation
│   ├── 00-MASTER.md             # System overview
│   ├── LLM-AGENT-GUIDE.md       # Task routing paths
│   ├── LAYER-01-DESIGN-TOKENS.md
│   ├── LAYER-02-COMPONENTS.md
│   ├── LAYER-08-ACCESSIBILITY.md
│   ├── PROMPTS/                 # Task templates
│   ├── APPENDICES/              # Reference material
│   ├── APPLICATION-DOMAIN/      # App patterns
│   └── MARKETING-DOMAIN/        # Marketing patterns
│
├── templates/                   # Project scaffolding
│   ├── project-config.yaml
│   └── APPLICATION-DOMAIN/
│
└── configs/                     # Dev tool configs
    ├── tsconfig.design-system.json
    └── prettierrc.json
```

## Design System Principles

All agents enforce these mandatory rules:

### 1. Semantic Tokens Only

```typescript
// NEVER
className="bg-slate-900 text-white"

// ALWAYS
className="bg-primary text-primary-foreground"
```

### 2. cva for Variants

```typescript
// NEVER
className={`btn ${isPrimary ? 'bg-blue-500' : 'bg-gray-500'}`}

// ALWAYS
const variants = cva("base", { variants: { ... } })
```

### 3. TypeScript Types

```typescript
// ALWAYS export interface with VariantProps
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}
```

### 4. Accessibility

```typescript
// ALWAYS for icon-only buttons
<button aria-label="Close"><X /></button>
```

## Token Quick Reference

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `bg-background` | White | Slate 900 | Page background |
| `bg-primary` | Blue 500 | Blue 400 | Primary buttons |
| `text-foreground` | Slate 900 | Slate 50 | Body text |
| `text-muted-foreground` | Slate 500 | Slate 400 | Secondary text |
| `border-border` | Slate 200 | Slate 700 | Borders |

## Documentation

- [GETTING-STARTED.md](./GETTING-STARTED.md) - Quick start guide
- [QUICK-REFERENCE.md](./QUICK-REFERENCE.md) - Token and pattern cheat sheet
- [PUBLISHING.md](./PUBLISHING.md) - Publishing to marketplace
- [ARCHITECTURE-V3.md](./ARCHITECTURE-V3.md) - Detailed architecture

## Contributing

1. Fork the repository
2. Clone to `~/.claude/plugins/design-system` (symlink recommended)
3. Make changes - they're immediately reflected
4. Submit a pull request

## License

MIT
