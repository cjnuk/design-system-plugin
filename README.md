# Design System Plugin for Claude Code

A context-efficient Claude Code plugin for building UI components with shadcn/ui, semantic tokens, and WCAG accessibility.

## Architecture

This plugin uses an **agent-centric architecture** to minimize main context consumption:

- **Thin Skills** (~25 lines): Trigger detection + agent dispatch only
- **Specialized Agents** (~60-100 lines): Load knowledge on-demand in isolated context
- **Knowledge Base** (~14k lines): Design system docs loaded by agents as needed

### Context Efficiency

| Architecture | Main Context Load |
|--------------|-------------------|
| Traditional (embedded skills) | ~300 lines per command |
| Agent-centric (this plugin) | ~25 lines per command |

**Result:** ~90% reduction in main session context usage.

## Installation

```bash
# Clone into Claude Code plugins directory
git clone https://github.com/cjnuk/design-system-plugin ~/.claude/plugins/design-system
```

Or symlink an existing clone:

```bash
ln -s /path/to/design-system-plugin ~/.claude/plugins/design-system
```

## Available Commands

| Command | Purpose | Agent |
|---------|---------|-------|
| `/ds-setup` | Initialize project with design system | setup-agent |
| `/ds-component [name]` | Generate component with cva, types, a11y | component-agent |
| `/ds-form-field [name]` | Add form field with Zod validation | form-agent |
| `/ds-accessibility` | Fix WCAG 2.1 AA violations | accessibility-agent |
| `/ds-dark-mode` | Implement dark mode theming | theming-agent |
| `/ds-tokens` | Design token reference and guidance | theming-agent |
| `/ds-review` | Review code against design system | review-agent |
| `/ds-migrate` | Migrate from MUI/Chakra/Bootstrap | migration-agent |
| `/design-system` | Intelligent routing for complex requests | router-agent |

## How It Works

1. **You invoke a skill** (e.g., `/ds-component Badge`)
2. **Thin skill dispatches to agent** (component-agent)
3. **Agent loads knowledge on-demand** via Read tool:
   - `knowledge/PROMPTS/01-NEW-COMPONENT.md`
   - `knowledge/LAYER-01-DESIGN-TOKENS.md`
   - `knowledge/LAYER-02-COMPONENTS.md`
4. **Agent executes in isolated context** - main session stays clean
5. **Agent returns result** - only the output reaches your session

## Directory Structure

```
design-system-plugin/
├── plugin.json
├── skills/                    # THIN ROUTERS (~25 lines each)
│   ├── design-system.md       # Master router
│   ├── setup.md
│   ├── component.md
│   ├── form-field.md
│   ├── accessibility.md
│   ├── dark-mode.md
│   ├── review.md
│   ├── tokens.md
│   └── migrate.md
├── agents/                    # SPECIALIZED AGENTS (load knowledge on-demand)
│   ├── router-agent.md        # Intelligent routing
│   ├── setup-agent.md
│   ├── component-agent.md
│   ├── form-agent.md
│   ├── accessibility-agent.md
│   ├── theming-agent.md
│   ├── review-agent.md
│   └── migration-agent.md
├── hooks/
│   └── validate-output.md     # PostToolUse validation
└── knowledge/                 # Design system documentation
    ├── LLM-AGENT-GUIDE.md     # Task routing paths
    ├── LAYER-01-DESIGN-TOKENS.md
    ├── LAYER-02-COMPONENTS.md
    ├── LAYER-08-ACCESSIBILITY.md
    ├── PROMPTS/               # Task templates
    └── ...
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

## License

MIT
