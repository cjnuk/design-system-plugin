---
skill: ds-init
description: Initialize design system in a project with scaffolded configuration and domain files
triggers:
  - "ds init"
  - "setup design system"
  - "initialize design system"
  - "design system init"
---

# Initialize Design System

Initialize the design system for a new project by creating project-specific configuration and domain pattern files.

## What This Does

Creates a project-scoped design system configuration in `.claude/design-system/` with:

1. Project metadata (framework, testing, stack)
2. Application domain patterns and component inventory
3. Setup and testing documentation
4. Implementation guidelines specific to your project

## Prerequisites

- Project has a `.claude/` directory (or Claude will create it)
- You're able to answer questions about your project setup

## Setup Process

### Step 1: Gather Project Information

I'll ask you the following:

1. **Project Name**: What's the name of your application?
2. **Project Type**: Application or Marketing site?
3. **Framework**: Next.js, Remix, or Vite?
4. **UI Library**: shadcn/ui, Tailwind UI, or both?
5. **State Management**: Jotai, Zustand, or none?
6. **Testing Framework**: Vitest, Jest, or Playwright?
7. **Any Custom Components**: Already built components to document?

### Step 2: Create Directory Structure

Claude will create:

```
.claude/
  design-system/
    project-config.yaml           # Project metadata
    APPLICATION-DOMAIN/
      09b-COMPONENTS.md           # Component inventory
      10b-SETUP.md                # Framework-specific setup
      11b-TESTING.md              # Testing strategy
      12b-IMPLEMENTATION.md       # Implementation patterns
```

### Step 3: Configure Files

Each file is pre-populated based on your answers but can be customized:

- **project-config.yaml**: YAML format with framework, testing, and component metadata
- **09b-COMPONENTS.md**: Scaffolded with your existing components (if any)
- **10b-SETUP.md**: Framework-specific patterns for your choice
- **11b-TESTING.md**: Testing approach for your test runner
- **12b-IMPLEMENTATION.md**: Implementation guidelines and patterns

### Step 4: Ready to Use

Once created, you can:

- Use `/ds-component` to create new components following your patterns
- Use `/ds-lint-setup` to configure ESLint for your project
- Use `/ds-review` to validate components against your patterns
- Customize files over time as patterns emerge

## File Format Examples

### project-config.yaml

```yaml
project:
  name: "MyApp"
  type: "application"

stack:
  framework: "next"
  ui_library: "shadcn"
  state_management: "jotai"
  testing:
    unit: "vitest"
    e2e: "playwright"

customizations: []

components:
  installed:
    - button
    - card
    - dialog
  custom: []
```

### 09b-COMPONENTS.md

Template for documenting installed and custom components:

```markdown
# Component Inventory

## Installed from shadcn/ui

- Button
- Card
- Dialog
- Form
- Input
- Label
- Select

## Custom Components

### DataTableAdvanced
Advanced table with sorting, filtering, pagination

### FileUploader
Multi-file upload component
```

### 10b-SETUP.md

Framework-specific setup patterns (Next.js, Remix, etc.)

### 11b-TESTING.md

Testing strategy and patterns for your chosen test runner

### 12b-IMPLEMENTATION.md

Project-specific implementation guidelines

## Integration with Other Skills

Once initialized, other skills automatically use your project configuration:

- **ds-component**: Follows your patterns from `12b-IMPLEMENTATION.md`
- **ds-review**: Validates against your component inventory
- **ds-lint-setup**: Configures based on your framework choice

## Updating Configuration

At any time, you can:

1. Edit `.claude/design-system/project-config.yaml` to update metadata
2. Add new components to `09b-COMPONENTS.md`
3. Update patterns in `12b-IMPLEMENTATION.md` as they evolve

## Next Steps After Initialization

```
1. Create first component: /ds-component
2. Set up linting: /ds-lint-setup
3. Start implementing: Review patterns in 12b-IMPLEMENTATION.md
4. Share with team: Commit .claude/design-system/ to git
```

## For Existing Projects

If you have an existing project with components already built, use the **audit-first workflow**:

1. Run `/ds-audit` first to analyze your codebase and generate audit report
2. Run `/ds-init` to complete project configuration (audit may have scaffolded some files)
3. Review `.claude/design-system/AUDIT-REPORT.md` for findings
4. Use `/ds-migrate` to plan incremental adoption based on audit

See `INSTALLATION.md` for the complete existing project workflow.

**Note:** `/ds-audit` should be your FIRST step for existing projects - it analyzes what you have before you configure.

## Troubleshooting

**Q: I don't see the `.claude/` directory**
A: Claude will create it automatically when initializing.

**Q: Can I change project config after initialization?**
A: Yes! Edit `.claude/design-system/project-config.yaml` anytime.

**Q: How do I share project patterns with my team?**
A: Commit `.claude/design-system/` to your git repository. Team members get automatic project context.

**Q: What if I need to add a new domain (e.g., MARKETING-DOMAIN)?**
A: Create a new directory in `.claude/design-system/MARKETING-DOMAIN/` with the same file structure.
