# Getting Started with the Design System Plugin

## Quick Answer

**Install the plugin globally ONCE**, then run `ds-init` for **EACH new project**.

You do NOT copy files manually. The plugin handles everything through a hybrid architecture:
- **Global plugin** (~/.claude/plugins/design-system/): Stable core, shared across all projects
- **Project config** (./.claude/design-system/): Customizable per project, committed to git

---

## One-Time Setup: Install the Plugin

Install the design system plugin to your global Claude plugins directory. You only do this once per machine.

### Option A: Clone from Repository (Recommended)

```bash
# Create plugins directory if it doesn't exist
mkdir -p ~/.claude/plugins

# Clone the plugin
cd ~/.claude/plugins
git clone https://github.com/cjnuk/design-system-plugin design-system

# Verify installation
ls ~/.claude/plugins/design-system/
```

You should see directories like `skills/`, `system/`, `templates/`, `configs/`.

### Option B: Symlink for Development

If you're developing the plugin itself, use a symlink:

```bash
# Assuming you have the plugin at /path/to/design-system-plugin
ln -s /path/to/design-system-plugin ~/.claude/plugins/design-system

# Verify it works
ls ~/.claude/plugins/design-system/
```

### Verify the Installation

In any Claude Code project, type:

```
/ds-init
```

If you see the skill available, the plugin is installed correctly.

---

## Per-Project Setup: Initialize with `ds-init`

Once the plugin is installed globally, initialize each new project with the `ds-init` skill.

### Step 1: Navigate to Your Project

```bash
cd /path/to/your/new-project
```

### Step 2: Run the Initialization Skill

In Claude Code, type:

```
ds init
```

Or use the full command:

```
/ds-init
```

### Step 3: Answer Project Questions

Claude will ask you about your project setup:

- **Project name**: What should we call this project?
- **Project type**: Application, component library, or documentation site?
- **Framework**: Next.js, Remix, Vite, or other?
- **UI library**: shadcn/ui, Tailwind UI, or both?
- **State management**: Jotai, Zustand, Redux, or none?
- **Testing**: Vitest, Jest, Playwright, or Cypress?

### Step 4: Project Configuration Created

The `ds-init` skill creates the following structure in your project:

```
.claude/design-system/
├── project-config.yaml
│   └── Your project metadata and choices
└── APPLICATION-DOMAIN/
    ├── 09b-COMPONENTS.md
    │   └── Component inventory (button, card, etc.)
    ├── 10b-SETUP.md
    │   └── Framework-specific setup instructions
    ├── 11b-TESTING.md
    │   └── Testing strategy for your stack
    └── 12b-IMPLEMENTATION.md
        └── Implementation patterns and guidelines
```

### Step 5: Start Using Design System Skills

Now you can use all 14 design system skills:

```
/ds-component Button        # Create a new component
/ds-review                  # Review your code
/ds-lint-setup              # Configure ESLint and Prettier
/ds-tokens                  # Query design tokens
```

---

## What Gets Created Where

### Global Installation: `~/.claude/plugins/design-system/`

This is installed once and shared across all your projects.

```
design-system/
├── .claude-plugin/
│   └── plugin.json                 # Plugin metadata for Claude Code
├── skills/                         # 14 reusable skills
│   ├── ds-init/SKILL.md           # Project initialization
│   ├── ds-component/SKILL.md      # Component creation
│   ├── ds-lint-setup/SKILL.md     # ESLint/Prettier setup
│   ├── ds-tokens/SKILL.md         # Token reference
│   ├── ds-accessibility/SKILL.md  # Accessibility patterns
│   ├── ds-animation/SKILL.md      # Motion patterns
│   ├── ds-compound-component/     # Complex components
│   ├── ds-data-table/             # TanStack Table patterns
│   ├── ds-dark-mode/              # Dark mode implementation
│   ├── ds-form-field/             # Form patterns
│   ├── ds-migrate/                # Migration helpers
│   ├── ds-review/                 # Code validation
│   ├── ds-virtualize/             # Virtual scrolling
│   └── ds-setup/                  # Legacy setup
├── templates/                      # Project templates
│   ├── project-config.yaml         # Config template
│   └── APPLICATION-DOMAIN/
│       ├── 09b-COMPONENTS.md
│       ├── 10b-SETUP.md
│       ├── 11b-TESTING.md
│       └── 12b-IMPLEMENTATION.md
├── system/                         # Stable knowledge base
│   ├── LAYER-00-STRATEGIC-DECISIONS.md
│   ├── LAYER-01-DESIGN-TOKENS.md
│   ├── LAYER-02-COMPONENTS.md
│   ├── LAYER-03-PATTERNS.md
│   ├── ... (13 layers total)
│   └── APPENDICES/
├── configs/                        # Configuration files
├── README.md                       # Full documentation
└── CHANGELOG.md                    # Version history
```

**Shared across all projects** • **Updated via `git pull`** • **Preserves project files automatically**

### Project Configuration: `./.claude/design-system/`

Created by `ds-init` in each project you initialize.

```
<your-project>/
├── .claude/
│   └── design-system/
│       ├── project-config.yaml
│       │   └── Your project metadata
│       │       (framework, testing, stack choices)
│       └── APPLICATION-DOMAIN/
│           ├── 09b-COMPONENTS.md
│           │   └── Your component inventory
│           │       (which components you're using)
│           ├── 10b-SETUP.md
│           │   └── Your framework setup
│           ├── 11b-TESTING.md
│           │   └── Your testing strategy
│           └── 12b-IMPLEMENTATION.md
│               └── Your implementation guidelines
```

**Project-specific** • **Committed to git** • **Shared with your team**

---

## Knowledge Priority

When you use design system skills, Claude checks for information in this order:

1. **Project patterns** → `./.claude/design-system/APPLICATION-DOMAIN/*.md`
2. **Skill reference** → Embedded in the skill definition
3. **Core layers** → `~/.claude/plugins/design-system/system/LAYER-*.md`

This means your project customizations take priority over the global defaults, allowing you to override patterns when needed.

---

## Workflow Summary

| Action | Command | Frequency | Location |
|--------|---------|-----------|----------|
| Install plugin | `git clone ~/.claude/plugins/design-system` | Once per machine | `~/.claude/plugins/` |
| Update plugin | `cd ~/.claude/plugins/design-system && git pull` | When updates available | `~/.claude/plugins/` |
| Initialize project | `/ds-init` | Once per project | `./.claude/design-system/` |
| Create component | `/ds-component [name]` | As needed | Project-specific |
| Review code | `/ds-review` | As needed | Project-specific |
| Query tokens | `/ds-tokens` | As needed | Project-specific |
| Configure linting | `/ds-lint-setup` | Once per project | Project-specific |

---

## Common Scenarios

### Scenario 1: Starting a New Project

```bash
# 1. Create your project
mkdir my-app && cd my-app
git init

# 2. Install plugin (if not already done on this machine)
# Skip if already at ~/.claude/plugins/design-system

# 3. Initialize design system
# In Claude Code, type: /ds-init

# 4. Answer questions about your stack

# 5. Start creating components
# In Claude Code, type: /ds-component Button
```

### Scenario 2: Onboarding a Team Member

```bash
# 1. They clone your project
git clone https://github.com/your-org/my-app

# 2. They install the plugin (once)
# In Claude Code, type: /ds-init
# Plugin is at ~/.claude/plugins/design-system

# 3. The project's .claude/design-system/ is already in git
# They get your project patterns automatically

# 4. They can immediately use design system skills
# In Claude Code, type: /ds-component Button
```

### Scenario 3: Updating the Plugin

```bash
# When the design system plugin is updated:
cd ~/.claude/plugins/design-system
git pull origin main

# Your project files are safe - nothing changes in ./.claude/design-system/
# Team members pull and get updated plugin automatically
```

### Scenario 4: Customizing Patterns for Your Project

```bash
# After running /ds-init, edit:
.claude/design-system/APPLICATION-DOMAIN/

# For example, update component patterns:
# .claude/design-system/APPLICATION-DOMAIN/09b-COMPONENTS.md

# Or update implementation guidelines:
# .claude/design-system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md

# Commit to git
git add .claude/design-system/
git commit -m "Customize design system patterns for our team"
git push
```

---

## FAQ

### Q: Do I copy any files manually?

**A:** No. The plugin installation and `ds-init` handle everything. Never copy files manually—use the initialization workflow instead.

### Q: Can I use the plugin with existing projects?

**A:** Yes. Run `/ds-init` in any existing project. Claude will scaffold the `.claude/design-system/` directory while preserving all your existing code.

### Q: How do I update the design system for my machine?

**A:**

```bash
cd ~/.claude/plugins/design-system
git pull origin main
```

Your project customizations in `./.claude/design-system/` are automatically preserved.

### Q: How does my team get the latest patterns?

**A:** Commit `.claude/design-system/` to your project's git repository:

```bash
# In your .gitignore - DO NOT ignore design-system:
!.claude/design-system/

# Commit and push
git add .claude/design-system/
git commit -m "Update design system patterns"
git push
```

Team members pull the repo and get the patterns automatically.

### Q: Can I customize patterns for my project?

**A:** Yes. After running `/ds-init`, edit the files in `.claude/design-system/APPLICATION-DOMAIN/`:

- **09b-COMPONENTS.md**: Your component inventory
- **10b-SETUP.md**: Your framework-specific setup
- **11b-TESTING.md**: Your testing strategy
- **12b-IMPLEMENTATION.md**: Your implementation guidelines

Changes here override the global defaults for your project.

### Q: What if I need project-specific tokens?

**A:** Edit `.claude/design-system/APPLICATION-DOMAIN/09b-COMPONENTS.md` to document your custom tokens. The design system skills will check your project patterns first.

### Q: How do I migrate from my old design system?

**A:** Use the `/ds-migrate` skill to convert existing components:

```
/ds-migrate
```

Claude will guide you through migrating from MUI, Chakra, Bootstrap, or other frameworks.

### Q: Can different projects have different configurations?

**A:** Yes. Each project gets its own `.claude/design-system/` directory. Run `/ds-init` in each project and answer questions specific to that project's stack.

### Q: What if my team uses different Claude Code versions?

**A:** The plugin works with Claude Code 1.0+. All team members should update to the latest Claude Code for the best experience.

### Q: How do I contribute new skills to the plugin?

**A:** See **Contributing & Extending** in the main README.md. Skills follow a standard format in `skills/ds-<name>/SKILL.md`.

---

## Next Steps

1. **Install the plugin** (one-time):
   ```bash
   cd ~/.claude/plugins
   git clone https://github.com/cjnuk/design-system-plugin design-system
   ```

2. **Initialize your first project**:
   ```
   /ds-init
   ```

3. **Create your first component**:
   ```
   /ds-component Button
   ```

4. **Review your code**:
   ```
   /ds-review
   ```

---

## Additional Resources

- **README.md**: Full architecture and features documentation
- **QUICK-REFERENCE.md**: Quick lookup for skills and commands
- **MIGRATION-GUIDE.md**: Upgrading from previous plugin versions
- **system/LAYER-00-STRATEGIC-DECISIONS.md**: Design system philosophy
- **system/LAYER-01-DESIGN-TOKENS.md**: Complete token reference

For detailed information about any skill, run:

```
/ds-review       # Understand code validation rules
/ds-tokens       # See all design tokens
/ds-init         # Reinitialize or see setup questions
```

---

**Version**: 2.2.0 | **Architecture**: Hybrid (ADR-001) | **Status**: Production Ready
