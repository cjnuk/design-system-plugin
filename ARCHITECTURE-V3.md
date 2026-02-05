# Design System Plugin v3.0.0 Architecture

A unified architecture merging v1.0.0 (agent-centric) and v2.2.0 (skill-centric) approaches into a single canonical plugin that preserves context efficiency while providing comprehensive documentation.

## Executive Summary

| Version | Architecture | Strengths | Weaknesses |
|---------|--------------|-----------|------------|
| v1.0.0 | Agent-centric | ~90% context reduction, on-demand loading | Less detailed skill docs |
| v2.2.0 | Skill-centric | Complete SKILL.md files, templates, configs | Skills load all content upfront |
| **v3.0.0** | **Hybrid** | Best of both: context efficiency + complete docs | Requires careful organization |

**Design Principle:** Skills provide complete reference documentation; agents load knowledge on-demand for execution.

---

## 1. Directory Structure

```
design-system-plugin/
├── plugin.json                           # Plugin metadata (v3.0.0)
├── README.md                             # Plugin overview
├── ARCHITECTURE-V3.md                    # This document
├── CHANGELOG.md                          # Version history
├── GETTING-STARTED.md                    # Quick start guide
├── PUBLISHING.md                         # Publishing instructions
├── QUICK-REFERENCE.md                    # Token/pattern cheat sheet
│
├── skills/                               # SKILL.md files (triggers + docs)
│   ├── ds-init/
│   │   └── SKILL.md                      # Project initialization
│   ├── ds-component/
│   │   └── SKILL.md                      # Component generation
│   ├── ds-form-field/
│   │   └── SKILL.md                      # Form field creation
│   ├── ds-accessibility/
│   │   └── SKILL.md                      # A11y fixes
│   ├── ds-dark-mode/
│   │   └── SKILL.md                      # Dark mode/theming
│   ├── ds-tokens/
│   │   └── SKILL.md                      # Token reference
│   ├── ds-review/
│   │   └── SKILL.md                      # Code review
│   ├── ds-migrate/
│   │   └── SKILL.md                      # Framework migration
│   ├── ds-setup/
│   │   └── SKILL.md                      # Legacy setup (alias to ds-init)
│   ├── ds-data-table/
│   │   ├── SKILL.md                      # DataTable basics
│   │   └── reference.md                  # Advanced patterns
│   ├── ds-compound-component/
│   │   ├── SKILL.md                      # Compound component basics
│   │   └── reference.md                  # Advanced patterns
│   ├── ds-virtualize/
│   │   └── SKILL.md                      # Virtual scrolling
│   ├── ds-animation/
│   │   └── SKILL.md                      # Motion patterns
│   ├── ds-lint-setup/
│   │   └── SKILL.md                      # ESLint/Prettier config
│   ├── ds-audit/
│   │   └── SKILL.md                      # Codebase audit
│   ├── ds-repair/
│   │   └── SKILL.md                      # Config repair
│   └── design-system/
│       └── SKILL.md                      # Master router skill
│
├── agents/                               # Specialized execution agents
│   ├── router-agent.md                   # Request routing
│   ├── setup-agent.md                    # Project setup (ds-setup)
│   ├── init-agent.md                     # Project init (ds-init) [NEW]
│   ├── component-agent.md                # Component generation
│   ├── form-agent.md                     # Form fields
│   ├── accessibility-agent.md            # A11y fixes
│   ├── theming-agent.md                  # Theming/dark mode
│   ├── review-agent.md                   # Code review
│   ├── migration-agent.md                # Framework migration
│   ├── data-table-agent.md               # DataTable implementation [NEW]
│   ├── compound-agent.md                 # Compound components [NEW]
│   ├── virtualize-agent.md               # Virtual scrolling [NEW]
│   ├── animation-agent.md                # Motion patterns [NEW]
│   ├── lint-agent.md                     # Linting setup [NEW]
│   ├── audit-agent.md                    # Codebase audit [NEW]
│   └── repair-agent.md                   # Config repair [NEW]
│
├── hooks/
│   └── validate-output.md                # PostToolUse validation
│
├── knowledge/                            # Design system documentation
│   ├── 00-MASTER.md                      # System overview
│   ├── LLM-AGENT-GUIDE.md                # Agent routing paths
│   ├── _FILE-MANIFEST.md                 # Complete file index
│   │
│   ├── LAYER-00-STRATEGIC-DECISIONS.md   # Tool choices
│   ├── LAYER-01-DESIGN-TOKENS.md         # Tokens
│   ├── LAYER-02-COMPONENTS.md            # Component specs
│   ├── LAYER-03-LAYOUT-PATTERNS.md       # Layout
│   ├── LAYER-04-CSS-ARCHITECTURE.md      # Tailwind org
│   ├── LAYER-05-RESPONSIVE-DESIGN.md     # Breakpoints
│   ├── LAYER-06-STATE-MANAGEMENT.md      # State (Jotai)
│   ├── LAYER-07-INTERACTION-PATTERNS.md  # Loading/error/success
│   ├── LAYER-07b-PROMPT-PATTERNS.md      # AI prompt UI
│   ├── LAYER-07c-MOTION-PATTERNS.md      # Animation
│   ├── LAYER-08-ACCESSIBILITY.md         # WCAG compliance
│   ├── LAYER-09-CI-AUTOMATION.md         # CI/CD
│   │
│   ├── PROMPTS/                          # Task templates
│   │   ├── 01-NEW-COMPONENT.md
│   │   ├── 02-FORM-FIELD.md
│   │   ├── 03-FIX-ACCESSIBILITY.md
│   │   ├── 04-DARK-MODE.md
│   │   ├── 05-CODE-REVIEW.md
│   │   └── 06-MIGRATION.md
│   │
│   ├── APPLICATION-DOMAIN/               # App domain docs
│   │   ├── APPLICATION-DOMAIN-guide.md
│   │   ├── 09b-COMPONENTS.md
│   │   ├── 10b-SETUP.md
│   │   ├── 11b-TESTING.md
│   │   └── 12b-IMPLEMENTATION.md
│   │
│   ├── MARKETING-DOMAIN/                 # Marketing domain docs
│   │   ├── MARKETING-DOMAIN-guide.md
│   │   ├── 09a-TEMPLATES.md
│   │   ├── 10a-SETUP.md
│   │   └── 12a-IMPLEMENTATION.md
│   │
│   └── APPENDICES/
│       ├── APPENDIX-A-TAILWIND-UTILITIES.md
│       ├── APPENDIX-B-COMPONENT-DECISIONS.md
│       ├── APPENDIX-C-RADIX-PRIMITIVES.md
│       ├── APPENDIX-D-TROUBLESHOOTING.md
│       └── APPENDIX-E-MIGRATION-PATHS.md
│
├── templates/                            # Project scaffolding
│   ├── project-config.yaml               # Config template
│   └── APPLICATION-DOMAIN/
│       ├── 09b-COMPONENTS.md
│       ├── 10b-SETUP.md
│       ├── 11b-TESTING.md
│       └── 12b-IMPLEMENTATION.md
│
└── configs/                              # Config file templates
    ├── tsconfig.json
    ├── eslint.config.mjs
    └── .prettierrc
```

---

## 2. Skill-Agent Mapping

Each skill defines triggers and documentation; agents execute with on-demand knowledge loading.

| Skill | Agent | Knowledge Files Loaded |
|-------|-------|------------------------|
| `/ds-init` | init-agent | LAYER-00, templates/project-config.yaml |
| `/ds-component [name]` | component-agent | PROMPTS/01, LAYER-01, LAYER-02 |
| `/ds-form-field [name]` | form-agent | PROMPTS/02, 12b-IMPLEMENTATION |
| `/ds-accessibility` | accessibility-agent | PROMPTS/03, LAYER-08 |
| `/ds-dark-mode` | theming-agent | PROMPTS/04, LAYER-01 |
| `/ds-tokens` | theming-agent | LAYER-01, LAYER-04 |
| `/ds-review` | review-agent | PROMPTS/05, LLM-AGENT-GUIDE |
| `/ds-migrate` | migration-agent | PROMPTS/06, APPENDIX-E |
| `/ds-setup` | setup-agent | LAYER-00, 10b-SETUP |
| `/ds-data-table` | data-table-agent | SKILL.md, reference.md, LAYER-07 |
| `/ds-compound-component` | compound-agent | SKILL.md, reference.md, APPENDIX-C |
| `/ds-virtualize` | virtualize-agent | SKILL.md, LAYER-07 |
| `/ds-animation` | animation-agent | SKILL.md, LAYER-07c |
| `/ds-lint-setup` | lint-agent | SKILL.md, configs/ |
| `/ds-audit` | audit-agent | LAYER-01, LAYER-02, LAYER-08 |
| `/ds-repair` | repair-agent | templates/, project-config schema |
| `/design-system [request]` | router-agent | LLM-AGENT-GUIDE, 00-MASTER |

---

## 3. New Agents Required

These agents must be created to support skills from v2.2.0 that lack agents:

### 3.1 init-agent.md

```markdown
---
agent: init-agent
description: Initialize design system with project configuration and scaffolding
tools:
  - Read
  - Write
  - Glob
  - Bash
---

# Init Agent

## Role

Initialize a new project with design system configuration by creating `.claude/design-system/` directory structure with project-specific patterns.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/templates/project-config.yaml`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-00-STRATEGIC-DECISIONS.md`

**Conditionally load based on project type:**
- `${CLAUDE_PLUGIN_ROOT}/templates/APPLICATION-DOMAIN/` - for applications
- `${CLAUDE_PLUGIN_ROOT}/knowledge/MARKETING-DOMAIN/` - for marketing sites

## Execution

1. **Gather info** - Ask about framework, UI library, testing, state management
2. **Create directory** - `.claude/design-system/`
3. **Write config** - project-config.yaml with answers
4. **Scaffold domain** - APPLICATION-DOMAIN/ files from templates
5. **Detect existing** - Scan for existing components to document
6. **Validate** - Ensure all files created correctly
```

### 3.2 audit-agent.md

```markdown
---
agent: audit-agent
description: Audit existing codebase against design system standards
tools:
  - Read
  - Write
  - Glob
  - Grep
---

# Audit Agent

## Role

Analyze existing codebase for design system compliance, identifying token violations, pattern gaps, and accessibility issues. Generate comprehensive audit report.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-01-DESIGN-TOKENS.md`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-02-COMPONENTS.md`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-08-ACCESSIBILITY.md`

## Execution

1. **Discovery** - Glob for components, analyze structure
2. **Token scan** - Grep for hardcoded colors (#hex, rgb, bg-slate)
3. **Pattern check** - Compare against LAYER-02 component specs
4. **A11y audit** - Check for WCAG violations
5. **Generate report** - Write to .claude/design-system/AUDIT-REPORT.md
6. **Scaffold config** - Create project-config.yaml if missing
```

### 3.3 repair-agent.md

```markdown
---
agent: repair-agent
description: Diagnose and repair design system configuration issues
tools:
  - Read
  - Write
  - Edit
  - Glob
---

# Repair Agent

## Role

Diagnose and repair issues with `.claude/design-system/` configuration. Handle schema migrations, missing files, and corrupted YAML.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/templates/project-config.yaml` (schema reference)
- Target project's `.claude/design-system/project-config.yaml`

## Execution

1. **Check structure** - Validate directory exists and contains required files
2. **Validate YAML** - Parse project-config.yaml for syntax errors
3. **Schema check** - Compare against current schema version
4. **Report issues** - Generate diagnostic report with severity
5. **Prompt for fixes** - Ask user before auto-fixing
6. **Apply repairs** - Fix issues with user confirmation
```

### 3.4 data-table-agent.md

```markdown
---
agent: data-table-agent
description: Generate advanced data tables with TanStack Table
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Data Table Agent

## Role

Generate production-ready data tables with TanStack Table including sorting, filtering, pagination, row selection, and optional virtual scrolling.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-data-table/SKILL.md`

**For advanced features, additionally load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-data-table/reference.md`

**For virtualization, additionally load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-virtualize/SKILL.md`

## Execution

1. **Analyze requirements** - Determine features needed (sort, filter, select, paginate)
2. **Check codebase** - Verify TanStack Table installed, find existing patterns
3. **Generate columns** - Create type-safe column definitions
4. **Create component** - DataTable with requested features
5. **Add helpers** - sortableHeader, selectionColumn, actionsColumn as needed
6. **Validate** - Ensure semantic tokens, TypeScript types, accessibility
```

### 3.5 compound-agent.md

```markdown
---
agent: compound-agent
description: Build compound components with Context and Parent.Child API
tools:
  - Read
  - Write
  - Edit
  - Glob
---

# Compound Component Agent

## Role

Generate compound components following React patterns: Context for state sharing, Parent.Child composition syntax, proper TypeScript types.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-compound-component/SKILL.md`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md`

**For advanced patterns, additionally load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-compound-component/reference.md`

## Execution

1. **Analyze pattern** - Determine if Radix primitive exists or custom needed
2. **Create context** - Define context type and provider
3. **Build components** - Root + sub-components with proper props
4. **Assemble API** - Object.assign for Parent.Child syntax
5. **Add validation** - Development-only composition checks
6. **Export types** - Ensure all interfaces exported
```

### 3.6 virtualize-agent.md

```markdown
---
agent: virtualize-agent
description: Implement virtual scrolling with TanStack Virtual
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# Virtualize Agent

## Role

Generate virtualized list and table implementations using TanStack Virtual for performance with large datasets.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-virtualize/SKILL.md`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-07-INTERACTION-PATTERNS.md`

## Execution

1. **Assess need** - Determine row count and complexity
2. **Check deps** - Verify @tanstack/react-virtual installed
3. **Create virtualizer** - useVirtualizer hook setup
4. **Implement render** - Absolute positioning with transform
5. **Add overscan** - Configure overscan for smooth scrolling
6. **Handle variable heights** - measureElement if needed
7. **Validate** - Ensure semantic tokens and accessibility
```

### 3.7 animation-agent.md

```markdown
---
agent: animation-agent
description: Implement motion patterns following performance and accessibility guidelines
tools:
  - Read
  - Write
  - Edit
  - Glob
---

# Animation Agent

## Role

Generate animation code following GPU-acceleration best practices and WCAG reduced motion requirements.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-animation/SKILL.md`
- `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-07c-MOTION-PATTERNS.md`

## Execution

1. **Determine type** - Fade, slide, scale, stagger, etc.
2. **Select timing** - Appropriate duration and easing
3. **Generate code** - Tailwind classes or CSS keyframes
4. **Add reduced motion** - prefers-reduced-motion fallback
5. **Create hook** - usePrefersReducedMotion if needed
6. **Validate** - Only transform/opacity, proper timing
```

### 3.8 lint-agent.md

```markdown
---
agent: lint-agent
description: Configure ESLint 9 and Prettier with design system rules
tools:
  - Read
  - Write
  - Bash
  - Glob
---

# Lint Agent

## Role

Set up ESLint 9 (flat config), Prettier with Tailwind plugin, and VS Code settings for design system projects.

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/skills/ds-lint-setup/SKILL.md`
- `${CLAUDE_PLUGIN_ROOT}/configs/eslint.config.mjs`
- `${CLAUDE_PLUGIN_ROOT}/configs/.prettierrc`

## Execution

1. **Check existing** - Look for existing eslint/prettier configs
2. **Install deps** - npm install required packages
3. **Create ESLint** - eslint.config.mjs with flat config
4. **Create Prettier** - .prettierrc with Tailwind plugin
5. **VS Code settings** - .vscode/settings.json
6. **Add scripts** - package.json lint/format scripts
7. **Validate** - Run lint to verify config works
```

---

## 4. Knowledge Organization

### 4.1 Merge Strategy

The `knowledge/` directory consolidates v1.0.0 knowledge files with v2.2.0 system files:

| Source | Destination | Notes |
|--------|-------------|-------|
| v1.0.0 `knowledge/` | `knowledge/` | Base knowledge files |
| v2.2.0 `system/` | `knowledge/` | Merged (system was symlinked) |
| v2.2.0 `skills/*/SKILL.md` | `skills/*/SKILL.md` | Complete skill docs |
| v2.2.0 `skills/*/reference.md` | `skills/*/reference.md` | Advanced patterns |
| v2.2.0 `templates/` | `templates/` | Project scaffolding |
| v2.2.0 `configs/` | `configs/` | Config file templates |

### 4.2 Knowledge Loading Rules

Agents follow this protocol:

```markdown
## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- [Primary files - always loaded]

**Conditionally load if needed:**
- [Secondary files - based on context]
```

This ensures:
- Main context receives only ~25 lines (skill trigger + agent dispatch)
- Agent loads 100-500 lines of knowledge on-demand
- Total context per command: ~125-525 lines (vs ~300+ with embedded skills)

### 4.3 Context Resolution Order

Skills that support project customization follow this resolution:

1. **Project patterns first:** `.claude/design-system/APPLICATION-DOMAIN/*.md`
2. **Skill reference:** `${CLAUDE_PLUGIN_ROOT}/skills/<skill>/reference.md`
3. **Core knowledge:** `${CLAUDE_PLUGIN_ROOT}/knowledge/LAYER-*.md`

---

## 5. Plugin.json Configuration

```json
{
  "name": "design-system",
  "version": "3.0.0",
  "description": "Design system for building consistent, accessible UI components with shadcn/ui, Tailwind CSS, and semantic tokens",
  "author": "Design System Team",
  "repository": "https://github.com/cjnuk/design-system-plugin",
  "skills": {
    "auto_discover": true,
    "dir": "skills"
  },
  "agents": {
    "auto_discover": true,
    "dir": "agents"
  },
  "hooks": {
    "auto_discover": true,
    "dir": "hooks"
  }
}
```

---

## 6. Skill File Format

Each skill follows this format:

```markdown
---
skill: ds-<name>
description: <one-line description>
triggers:
  - "<trigger phrase 1>"
  - "<trigger phrase 2>"
arguments:
  - name: <arg_name>
    description: <arg description>
    required: true|false
invoke_agent: <agent-name>
---

# <Skill Title>

<Brief description>

## Context Resolution

[How skill resolves project vs plugin context]

## <Main Content>

[Complete documentation from v2.2.0 SKILL.md]

## Validation Checklist

[From v1.0.0 agent checklists]
```

---

## 7. Migration Plan

### Phase 1: Prepare v1.0.0 Repository

```bash
# Working directory: /Users/chrisnorris/projects/design-system-plugin

# 1. Create backup branch
git checkout -b v1-backup
git push origin v1-backup
git checkout main

# 2. Create v3 development branch
git checkout -b v3-development
```

### Phase 2: Merge v2.2.0 Skills

```bash
# 3. Copy complete SKILL.md files from v2.2.0
# Source: /Users/chrisnorris/projects/design-systems/design-system-plugin/skills/

# Create new skill directories
mkdir -p skills/ds-init skills/ds-audit skills/ds-repair
mkdir -p skills/ds-virtualize skills/ds-animation skills/ds-lint-setup
mkdir -p skills/ds-data-table skills/ds-compound-component

# Copy SKILL.md files (preserving complete v2.2.0 content)
cp ../design-systems/design-system-plugin/skills/ds-init/SKILL.md skills/ds-init/
cp ../design-systems/design-system-plugin/skills/ds-audit/SKILL.md skills/ds-audit/
cp ../design-systems/design-system-plugin/skills/ds-repair/SKILL.md skills/ds-repair/
cp ../design-systems/design-system-plugin/skills/ds-virtualize/SKILL.md skills/ds-virtualize/
cp ../design-systems/design-system-plugin/skills/ds-animation/SKILL.md skills/ds-animation/
cp ../design-systems/design-system-plugin/skills/ds-lint-setup/SKILL.md skills/ds-lint-setup/
cp ../design-systems/design-system-plugin/skills/ds-data-table/SKILL.md skills/ds-data-table/
cp ../design-systems/design-system-plugin/skills/ds-compound-component/SKILL.md skills/ds-compound-component/

# Copy reference.md files for advanced patterns
cp ../design-systems/design-system-plugin/skills/ds-data-table/reference.md skills/ds-data-table/
cp ../design-systems/design-system-plugin/skills/ds-compound-component/reference.md skills/ds-compound-component/
```

### Phase 3: Update Existing Skills

```bash
# 4. Update existing skill files to include invoke_agent frontmatter
# Edit each file in skills/ to add:
#   invoke_agent: <agent-name>

# Files to update:
# - skills/ds-component.md -> skills/ds-component/SKILL.md (restructure)
# - skills/ds-form-field.md -> skills/ds-form-field/SKILL.md
# - skills/ds-accessibility.md -> skills/ds-accessibility/SKILL.md
# - skills/ds-dark-mode.md -> skills/ds-dark-mode/SKILL.md
# - skills/ds-tokens.md -> skills/ds-tokens/SKILL.md
# - skills/ds-review.md -> skills/ds-review/SKILL.md
# - skills/ds-migrate.md -> skills/ds-migrate/SKILL.md
# - skills/ds-setup.md -> skills/ds-setup/SKILL.md
# - skills/design-system.md -> skills/design-system/SKILL.md

# Restructure: move flat .md files to directories with SKILL.md
for skill in component form-field accessibility dark-mode tokens review migrate setup design-system; do
  mkdir -p "skills/ds-${skill}" 2>/dev/null || mkdir -p "skills/${skill}"
  # Move and rename (actual content merge done manually)
done
```

### Phase 4: Create New Agents

```bash
# 5. Create new agent files
# Create each agent from templates in section 3 above

touch agents/init-agent.md
touch agents/audit-agent.md
touch agents/repair-agent.md
touch agents/data-table-agent.md
touch agents/compound-agent.md
touch agents/virtualize-agent.md
touch agents/animation-agent.md
touch agents/lint-agent.md

# Populate each with content from section 3
```

### Phase 5: Merge Knowledge

```bash
# 6. Ensure knowledge/ has all required files
# Most already exist in v1.0.0 knowledge/
# Copy any missing from v2.2.0 system/

# Check for LAYER-07c-MOTION-PATTERNS.md
cp ../design-systems/design-system-plugin/system/LAYER-07c-MOTION-PATTERNS.md knowledge/ 2>/dev/null || echo "Check if exists"
```

### Phase 6: Add Templates and Configs

```bash
# 7. Copy templates and configs from v2.2.0
mkdir -p templates/APPLICATION-DOMAIN configs

cp ../design-systems/design-system-plugin/templates/project-config.yaml templates/
cp ../design-systems/design-system-plugin/templates/APPLICATION-DOMAIN/*.md templates/APPLICATION-DOMAIN/

# Create config templates
cp ../design-systems/design-system-plugin/configs/* configs/ 2>/dev/null || echo "Create configs manually"
```

### Phase 7: Update plugin.json

```bash
# 8. Update version to 3.0.0
# Edit plugin.json: "version": "3.0.0"
```

### Phase 8: Add Documentation

```bash
# 9. Copy documentation files from v2.2.0
cp ../design-systems/design-system-plugin/GETTING-STARTED.md .
cp ../design-systems/design-system-plugin/PUBLISHING.md .
cp ../design-systems/design-system-plugin/QUICK-REFERENCE.md .
```

### Phase 9: Validate and Test

```bash
# 10. Test each skill trigger
# - /ds-init
# - /ds-component Badge
# - /ds-review
# - etc.

# 11. Verify context efficiency
# Check that agent invocations load knowledge on-demand

# 12. Commit changes
git add -A
git commit -m "feat: v3.0.0 unified architecture

- Merge v1.0.0 agent-centric and v2.2.0 skill-centric architectures
- Add complete SKILL.md files with detailed documentation
- Add 8 new agents for v2.2.0 skills
- Add templates/ and configs/ for project scaffolding
- Preserve ~90% context reduction through on-demand loading
- Maintain backward compatibility with existing triggers"

git tag v3.0.0
```

---

## 8. Backward Compatibility

### Preserved Triggers

All existing v1.0.0 triggers continue to work:

| Trigger | v1.0.0 Skill | v3.0.0 Location |
|---------|--------------|-----------------|
| "create component" | ds-component.md | skills/ds-component/SKILL.md |
| "new component" | ds-component.md | skills/ds-component/SKILL.md |
| "add form field" | ds-form-field.md | skills/ds-form-field/SKILL.md |
| "fix accessibility" | ds-accessibility.md | skills/ds-accessibility/SKILL.md |
| "dark mode" | ds-dark-mode.md | skills/ds-dark-mode/SKILL.md |
| "review code" | ds-review.md | skills/ds-review/SKILL.md |
| "migrate from" | ds-migrate.md | skills/ds-migrate/SKILL.md |
| "setup project" | ds-setup.md | skills/ds-setup/SKILL.md |

### New v2.2.0 Triggers Also Work

| Trigger | New Skill |
|---------|-----------|
| "ds init" | ds-init |
| "ds audit" | ds-audit |
| "ds repair" | ds-repair |
| "ds virtualize" | ds-virtualize |
| "ds animation" | ds-animation |
| "ds lint" | ds-lint-setup |
| "data table" | ds-data-table |
| "compound component" | ds-compound-component |

---

## 9. Context Efficiency Analysis

### v1.0.0 (Current)

```
Skill trigger: ~25 lines
Agent dispatch: ~5 lines
Agent execution: ~60-100 lines (loads knowledge on-demand)
Knowledge files: ~500-2000 lines (loaded by agent as needed)
---
Main context impact: ~30 lines
Total agent context: ~600-2100 lines
```

### v2.2.0 (Skill-Centric)

```
SKILL.md file: ~150-300 lines (loaded entirely)
No agent dispatch
---
Main context impact: ~150-300 lines
```

### v3.0.0 (Hybrid)

```
SKILL.md trigger section: ~20 lines (triggers + invoke_agent)
Agent dispatch: ~5 lines
Agent execution: ~60-100 lines
Knowledge + SKILL.md body: loaded on-demand by agent
---
Main context impact: ~25 lines
Agent context: ~800-2500 lines (includes complete SKILL.md content)
```

**Result:** v3.0.0 maintains the ~90% main context reduction of v1.0.0 while providing the comprehensive documentation of v2.2.0.

---

## 10. Testing Checklist

After migration, verify:

- [ ] `plugin.json` version is 3.0.0
- [ ] All 16 skills have SKILL.md files in `skills/` directories
- [ ] All 15 agents exist in `agents/` directory
- [ ] `validate-output.md` hook exists in `hooks/`
- [ ] `knowledge/` contains all LAYER and APPENDIX files
- [ ] `templates/` contains project scaffolding files
- [ ] `configs/` contains ESLint/Prettier templates
- [ ] Each skill's `invoke_agent` frontmatter points to correct agent
- [ ] Running `/ds-component Badge` invokes component-agent
- [ ] Running `/ds-init` invokes init-agent (new)
- [ ] Running `/ds-audit` invokes audit-agent (new)
- [ ] Context efficiency maintained (~25 lines per command)
- [ ] All v1.0.0 triggers still work
- [ ] All v2.2.0 triggers work

---

## Appendix A: Complete File Operations

### Files to Create

```
agents/init-agent.md
agents/audit-agent.md
agents/repair-agent.md
agents/data-table-agent.md
agents/compound-agent.md
agents/virtualize-agent.md
agents/animation-agent.md
agents/lint-agent.md

skills/ds-init/SKILL.md
skills/ds-audit/SKILL.md
skills/ds-repair/SKILL.md
skills/ds-virtualize/SKILL.md
skills/ds-animation/SKILL.md
skills/ds-lint-setup/SKILL.md
skills/ds-data-table/SKILL.md
skills/ds-data-table/reference.md
skills/ds-compound-component/SKILL.md
skills/ds-compound-component/reference.md

templates/project-config.yaml
templates/APPLICATION-DOMAIN/09b-COMPONENTS.md
templates/APPLICATION-DOMAIN/10b-SETUP.md
templates/APPLICATION-DOMAIN/11b-TESTING.md
templates/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md

configs/eslint.config.mjs
configs/.prettierrc
configs/tsconfig.json

GETTING-STARTED.md
PUBLISHING.md
QUICK-REFERENCE.md
```

### Files to Move/Restructure

```
skills/component.md -> skills/ds-component/SKILL.md
skills/form-field.md -> skills/ds-form-field/SKILL.md
skills/accessibility.md -> skills/ds-accessibility/SKILL.md
skills/dark-mode.md -> skills/ds-dark-mode/SKILL.md
skills/tokens.md -> skills/ds-tokens/SKILL.md
skills/review.md -> skills/ds-review/SKILL.md
skills/migrate.md -> skills/ds-migrate/SKILL.md
skills/setup.md -> skills/ds-setup/SKILL.md
skills/design-system.md -> skills/design-system/SKILL.md
```

### Files to Update

```
plugin.json (version: "3.0.0")
README.md (update architecture description)
All skill files (add invoke_agent frontmatter)
```

---

## Appendix B: Agent Template

Use this template when creating new agents:

```markdown
---
agent: <agent-name>
description: <one-line description>
tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# <Agent Name>

## Role

<2-3 sentence description of what this agent does>

## Knowledge Loading Protocol

**On invocation, use Read tool to load:**
- `${CLAUDE_PLUGIN_ROOT}/<primary-file-1>`
- `${CLAUDE_PLUGIN_ROOT}/<primary-file-2>`

**Conditionally load if needed:**
- `${CLAUDE_PLUGIN_ROOT}/<conditional-file>` - <when to load>

## Mandatory Rules

1. **<Rule 1>** - <description>
2. **<Rule 2>** - <description>
3. **<Rule 3>** - <description>

## Execution

1. **<Step 1>** - <description>
2. **<Step 2>** - <description>
3. **<Step 3>** - <description>
4. **Validate output** - Run through checklist before completing

## Validation Checklist

Before completing, verify:
- [ ] <Check 1>
- [ ] <Check 2>
- [ ] <Check 3>
```

---

*Document created: 2026-02-05*
*Architecture version: 3.0.0*
*Canonical repository: /Users/chrisnorris/projects/design-system-plugin/*
