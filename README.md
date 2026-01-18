# Design System Plugin for Claude Code

A comprehensive Claude Code plugin that provides slash commands, agents, and hooks for building UI components following design system best practices.

## Features

- **8 Skills (Slash Commands)**: Quick access to design system guidance
- **1 Agent**: Deep component generation with full context
- **1 Hook**: Automated validation of generated code
- **Knowledge Base**: Full design system documentation

## Installation

### Option 1: Clone as a Plugin

```bash
# Clone the plugin into your Claude Code plugins directory
git clone https://github.com/your-org/design-system-plugin ~/.claude/plugins/design-system
```

### Option 2: Symlink Existing Repo

```bash
# If you already have the design system repo
ln -s /path/to/design-systems/design-system-plugin ~/.claude/plugins/design-system
```

### Option 3: Project-Local Installation

```bash
# Add to a specific project
mkdir -p .claude/plugins
cp -r /path/to/design-system-plugin .claude/plugins/design-system
```

## Available Skills

| Command | Description |
|---------|-------------|
| `/ds-setup` | Initialize a project with design system tokens and shadcn/ui |
| `/ds-component [name]` | Generate a new component following all patterns |
| `/ds-form-field [name]` | Add a form field with validation |
| `/ds-accessibility` | Fix accessibility issues |
| `/ds-dark-mode` | Implement dark mode |
| `/ds-review` | Review code against design system standards |
| `/ds-tokens` | Quick reference for design tokens |
| `/ds-migrate` | Migrate from MUI/Chakra/Bootstrap |

## Usage Examples

### Setup a New Project

```
/ds-setup
```

This will guide you through:
1. Installing dependencies (cva, clsx, tailwind-merge)
2. Configuring Tailwind with semantic tokens
3. Setting up shadcn/ui
4. Creating the `cn()` utility

### Generate a Component

```
/ds-component Badge
```

Creates a Badge component with:
- cva variant definitions
- TypeScript props interface
- Semantic tokens
- Accessibility attributes

### Add a Form Field

```
/ds-form-field email
```

Generates:
- Zod validation schema
- react-hook-form integration
- Accessible FormField component
- Error message handling

### Review Code

```
/ds-review
```

Checks code for:
- Hardcoded colors (should use semantic tokens)
- Missing accessibility attributes
- Improper TypeScript types
- Component pattern violations

## Agent: Component Builder

For complex components, use the component-builder agent:

```
Create a complex DataTable component with sorting, filtering,
and pagination. Should follow the design system patterns.
```

The agent will:
1. Analyze requirements
2. Check existing patterns in your codebase
3. Generate the component with proper structure
4. Create tests with jest-axe
5. Validate against all rules

## Output Validation Hook

The `validate-output` hook automatically checks generated `.tsx` and `.jsx` files for:

- Hardcoded colors → suggests semantic tokens
- Clickable divs → suggests buttons
- Missing aria-labels → warns about accessibility
- Any types → requires proper TypeScript

## Design System Principles

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
const variants = cva("base", { variants: { variant: { primary: "...", secondary: "..." } } })
className={cn(variants({ variant }))}
```

### 3. Accessibility First

```typescript
// NEVER
<div onClick={handleClick}>Click me</div>
<button><X /></button>

// ALWAYS
<button type="button" onClick={handleClick}>Click me</button>
<button aria-label="Close"><X /></button>
```

### 4. TypeScript Types

```typescript
// NEVER
function Component(props: any) {}

// ALWAYS
export interface ComponentProps extends VariantProps<typeof componentVariants> {
  title: string
}
```

## Directory Structure

```
design-system-plugin/
├── plugin.json              # Plugin manifest
├── README.md                # This file
├── skills/                  # Slash commands
│   ├── setup.md            # /ds-setup
│   ├── component.md        # /ds-component
│   ├── form-field.md       # /ds-form-field
│   ├── accessibility.md    # /ds-accessibility
│   ├── dark-mode.md        # /ds-dark-mode
│   ├── review.md           # /ds-review
│   ├── tokens.md           # /ds-tokens
│   └── migrate.md          # /ds-migrate
├── agents/
│   └── component-builder.md # Deep component generation
├── hooks/
│   └── validate-output.md   # Post-write validation
└── knowledge/              # Symlink to system/ docs
    ├── LLM-AGENT-GUIDE.md
    ├── LAYER-01-DESIGN-TOKENS.md
    ├── LAYER-02-COMPONENTS.md
    └── ...
```

## Token Quick Reference

| Token | Light | Dark | Usage |
|-------|-------|------|-------|
| `bg-background` | White | Slate 900 | Page background |
| `bg-primary` | Blue 500 | Blue 400 | Primary buttons |
| `text-foreground` | Slate 900 | Slate 50 | Body text |
| `text-muted-foreground` | Slate 500 | Slate 400 | Secondary text |
| `border-border` | Slate 200 | Slate 700 | Borders |

## Contributing

1. Follow the existing skill/agent patterns
2. Test all changes with `/ds-review`
3. Ensure accessibility compliance
4. Update this README for new features

## License

MIT
