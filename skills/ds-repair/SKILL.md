---
skill: ds-repair
description: Diagnose and repair design system configuration issues in project files
triggers:
  - "ds repair"
  - "repair design system"
  - "fix design system"
  - "design system broken"
  - "ds config broken"
  - "ds health check"
arguments:
  - name: mode
    description: "check" for diagnosis only, "fix" for auto-repair (default: check)
    required: false
  - name: verbose
    description: Show detailed diagnostic output
    required: false
---

# Design System Repair

Diagnose and repair issues with project-level design system configuration stored in `.claude/design-system/`. Handles corrupted, outdated, or misconfigured files created by `/ds-init`.

## Context Resolution

Before executing, check for project state:

1. **Check `.claude/` directory exists** - Project may not use Claude Code if missing
2. **Read `.claude/design-system/project-config.yaml`** - Primary configuration file
3. **Scan `.claude/design-system/APPLICATION-DOMAIN/`** - Domain pattern files
4. **Reference templates** - `${CLAUDE_PLUGIN_ROOT}/templates/` for repair sources

## Mode Selection

When triggered, determine the mode:

| User Request | Mode | Behavior |
|--------------|------|----------|
| "ds health check", "check design system" | `check` | Diagnose only, no changes |
| "ds repair", "fix design system" | `fix` | Auto-fix safe issues + prompt for others |
| Explicit `mode=check` | `check` | Diagnose only |
| Explicit `mode=fix` | `fix` | Auto-fix + prompt |

**Default mode is `check`** - Always run diagnosis first before offering fixes.

## Expected File Structure

Validate this structure exists and is well-formed:

```
.claude/
  design-system/
    project-config.yaml           # Required - project metadata
    APPLICATION-DOMAIN/           # Required - domain directory
      09b-COMPONENTS.md           # Required - component inventory
      10b-SETUP.md                # Required - framework setup
      11b-TESTING.md              # Required - testing strategy
      12b-IMPLEMENTATION.md       # Required - implementation patterns
    AUDIT-REPORT.md               # Optional - from ds-audit
```

---

## Phase 1: Detection

### Step 1: Check Directory Structure

Use Glob tool (NOT find) to verify structure:

```
1. Glob: .claude/design-system/**/*
2. Check for required directories and files
3. Note any extra files (may be intentional)
```

**Decision Tree:**

```
Is .claude/ directory present?
├── NO → CRITICAL: "Design system not initialized. Run /ds-init first."
│        ABORT repair (cannot continue)
│
└── YES → Is .claude/design-system/ present?
          ├── NO → CRITICAL: Can be created (safe auto-fix)
          │
          └── YES → Continue to file checks
```

### Step 2: Check Required Files

For each required file, use Read tool (NOT cat):

| File | If Missing | Severity |
|------|------------|----------|
| `project-config.yaml` | CRITICAL - Must prompt to create | CRITICAL |
| `APPLICATION-DOMAIN/` directory | HIGH - Can create from template | HIGH |
| `09b-COMPONENTS.md` | MEDIUM - Can create from template | MEDIUM |
| `10b-SETUP.md` | MEDIUM - Can create from template | MEDIUM |
| `11b-TESTING.md` | MEDIUM - Can create from template | MEDIUM |
| `12b-IMPLEMENTATION.md` | MEDIUM - Can create from template | MEDIUM |

### Step 3: Validate project-config.yaml

**3a. YAML Syntax Check**

Read file content and validate YAML structure:

```
Is YAML parseable?
├── NO → CRITICAL: Report syntax error with location
│        Provide recovery options (see Error Recovery section)
│
└── YES → Continue to schema validation
```

**3b. Schema Validation**

Validate against this schema:

```yaml
# Required top-level keys
project:
  name: string (required)
  type: enum ["application", "marketing", "hybrid"] (required)

stack:
  framework: enum ["next", "remix", "vite"] (required)
  ui_library: enum ["shadcn", "tailwind-ui", "both"] (required)
  state_management: enum ["jotai", "zustand", "none"] (required)
  testing:
    unit: enum ["vitest", "jest"] (required)
    e2e: enum ["playwright", "cypress"] (required)

customizations: array (optional, default: [])

components:
  installed: array of strings (required)
  custom: array of strings (optional, default: [])
```

**Validation Checks:**

| Check | If Failed | Severity |
|-------|-----------|----------|
| Missing `project.name` | HIGH - Must prompt for value | HIGH |
| Missing `project.type` | HIGH - Must prompt for value | HIGH |
| Missing `stack.framework` | HIGH - Can auto-detect from package.json | HIGH |
| Invalid enum value | HIGH - Suggest closest valid value | HIGH |
| Unknown top-level key | MEDIUM - May be intentional extension | MEDIUM |
| Missing `components.installed` | MEDIUM - Initialize as empty array | MEDIUM |
| Duplicate array entries | LOW - Auto-fix by removing duplicates | LOW |

### Step 4: Detect Schema Version

Check for outdated schema formats:

```
Does config have `framework` at root level (not under `stack`)?
├── YES → v1.x format detected (legacy)
│
└── NO → Does config have `stack.testing` as string (not object)?
          ├── YES → v2.0 format detected (outdated)
          │
          └── NO → v2.2 format (current) - No migration needed
```

**Version Indicators:**

| Version | Indicators |
|---------|------------|
| v1.x | `framework` at root level, no `stack` object |
| v2.0 | `stack.framework` exists, `stack.testing` is a string |
| v2.2 (current) | `stack.testing.unit` and `stack.testing.e2e` are separate |

### Step 5: Check References

For each component in `components.installed`:

```
1. Validate naming convention (should be kebab-case)
2. Optionally verify file exists at src/components/ui/<name>.tsx
3. Check for duplicates
```

For APPLICATION-DOMAIN files:

```
1. Check if referenced components exist in inventory
2. Note any non-standard files (INFO level)
```

---

## Phase 2: Report Generation

### Diagnostic Report Format

Generate report in this format:

```markdown
## Design System Health Check

**Status**: [HEALTHY | NEEDS REPAIR | CRITICAL]
**Config Version**: v2.2 (current) | v2.0 (outdated) | v1.x (legacy)
**Last Modified**: [timestamp from file]

### Summary

| Category | Status | Issues |
|----------|--------|--------|
| Files | [OK/WARN/ERROR] | [count] |
| Schema | [OK/WARN/ERROR] | [count] |
| References | [OK/WARN/ERROR] | [count] |
| Consistency | [OK/WARN/ERROR] | [count] |

### Issues Found

#### CRITICAL
[List critical issues or "None"]

#### HIGH
[List high severity issues]

#### MEDIUM
[List medium severity issues]

#### LOW
[List low severity issues]

#### INFO
[List informational findings]

### Recommended Actions

[Numbered list of recommended fixes]
```

### Severity Definitions

| Severity | Definition | Examples |
|----------|------------|----------|
| CRITICAL | Blocks design system usage | Missing config file, unparseable YAML |
| HIGH | Major functionality impaired | Missing required fields, invalid enum values |
| MEDIUM | Reduced functionality | Missing domain files, outdated schema |
| LOW | Minor issues | Duplicate entries, orphaned references |
| INFO | Informational | Non-standard files, unused components |

---

## Phase 3: Repair Actions

### Auto-Fix Actions (No Confirmation Required)

These are safe, non-destructive repairs that can be applied automatically:

| Issue | Action | Safety |
|-------|--------|--------|
| Missing `.claude/design-system/` directory | Create directory | Safe - no data loss |
| Duplicate entries in arrays | Remove duplicates | Safe - preserves unique values |
| Trailing whitespace in YAML values | Trim values | Safe - no semantic change |
| Missing optional arrays | Initialize as `[]` | Safe - valid empty default |
| Component name not kebab-case | Report only (do not auto-fix) | Naming may be intentional |

### Prompted Repairs (Require User Confirmation)

Always prompt before:

| Issue | Prompt | Options |
|-------|--------|---------|
| Missing `project-config.yaml` | "Create project-config.yaml?" | [C]reate / [S]kip / [A]bort |
| Missing APPLICATION-DOMAIN files | "Create [file] from template?" | [C]reate / [S]kip / [A]bort |
| Unknown keys in config | "Remove unknown key '[key]'?" | [Y]es / [N]o / [A]bort |
| Schema migration needed | "Migrate from v[X] to v2.2?" | [Y]es / [N]o / [A]bort |
| Component in config but file missing | "Remove '[name]' from installed list?" | [Y]es / [N]o |

### Migration Transformations

**v1.x to v2.2:**

```yaml
# Before (v1.x)
framework: next
testing: vitest

# After (v2.2)
stack:
  framework: next
  ui_library: shadcn  # User prompted
  state_management: none  # User prompted
  testing:
    unit: vitest
    e2e: playwright  # User prompted
```

**v2.0 to v2.2:**

```yaml
# Before (v2.0)
stack:
  framework: next
  testing: vitest

# After (v2.2)
stack:
  framework: next
  ui_library: shadcn  # User prompted if missing
  state_management: none  # User prompted if missing
  testing:
    unit: vitest
    e2e: playwright  # User prompted
```

### Repair Workflow

```
For each issue (by severity, highest first):

Is this an auto-fix issue?
├── YES → Apply fix silently, log to report
│
└── NO → Is mode = fix?
          ├── NO → Log to report as "Needs manual fix"
          │
          └── YES → Prompt user for action
                    ├── Create/Yes → Apply fix, log to report
                    ├── Skip/No → Log as "Skipped by user"
                    └── Abort → Stop repair, output partial report
```

---

## Phase 4: Repair Report

### Repair Report Format

After repairs, generate:

```markdown
## Design System Repair Report

**Mode**: [check | fix]
**Started**: [timestamp]
**Completed**: [timestamp]

### Actions Taken

| # | Issue | Action | Result |
|---|-------|--------|--------|
| 1 | [Issue description] | [Action taken] | [FIXED/SKIPPED/FAILED] |
| 2 | ... | ... | ... |

### Files Modified

- [file path] ([N] changes)
- [file path] (created)

### Post-Repair Status

**Status**: [HEALTHY | NEEDS REPAIR]
**Remaining Issues**: [count or "None"]

### Next Steps

[Context-specific recommendations]
```

---

## Safety Guardrails

### Never Auto-Fix

| Situation | Reason | Action |
|-----------|--------|--------|
| YAML with unrecoverable syntax errors | Cannot parse structure | Abort, show error with line number |
| Custom fields that might be intentional | Could be user extensions | Prompt before removal |
| Files with uncommitted git changes | Could lose user work | Warn and prompt |
| Non-standard domain directories | Could be intentional (MARKETING-DOMAIN) | Report as INFO, do not modify |

### Abort Conditions

Immediately abort and report if:

1. **No `.claude/` directory** - Project may not use Claude Code
2. **Binary files detected** in design-system directory
3. **File permissions error** - Cannot write to `.claude/design-system/`
4. **YAML circular references** - Anchors/aliases causing loops

### Git Safety Check

Before modifying any file:

```
Is git initialized?
├── NO → Create backup in .claude/design-system/.backup/[timestamp]/
│
└── YES → Are there uncommitted changes to this file?
          ├── YES → Warn: "Uncommitted changes in [file]. Recommend committing first."
          │         Prompt: "Proceed anyway? [Y/n]"
          │
          └── NO → Safe to modify
```

---

## Error Recovery

### YAML Syntax Error

When YAML cannot be parsed:

```
Error: YAML syntax error at line [N]

The project-config.yaml file has invalid syntax:
  [error message from parser]

Options:
1. Fix manually: Edit .claude/design-system/project-config.yaml line [N]
2. Reset config: Run /ds-init to recreate from scratch (will prompt for values)
3. Restore from git: git checkout .claude/design-system/project-config.yaml
```

### Missing Directory

When `.claude/design-system/` is missing entirely:

```
Design system not initialized.

Options:
1. Run /ds-init to set up design system for this project
2. If you had a config before, check git history for .claude/design-system/
```

### Permission Denied

When cannot write to files:

```
Cannot write to .claude/design-system/

Check that:
- You have write permission to the .claude/ directory
- No other process has the file locked
- Disk is not full
```

---

## Auto-Detection for Missing Values

When creating or repairing config, auto-detect from project:

| Field | Detection Method |
|-------|------------------|
| `project.name` | Read `package.json` → `name` field |
| `stack.framework` | Check for `next`, `remix`, `vite` in `package.json` dependencies |
| `stack.testing.unit` | Check for `vitest.config.*` or `jest.config.*` |
| `stack.testing.e2e` | Check for `playwright.config.*` or `cypress.config.*` |

**Detection order:**
1. Try auto-detection first
2. If detection fails, prompt user
3. Provide sensible defaults if user skips

---

## Integration with Other Skills

### After Repair, Suggest Next Steps

Based on what was fixed:

| Repair Type | Suggested Action |
|-------------|------------------|
| Schema migrated | "Run `/ds-review` to ensure components match updated config" |
| Files recreated | "Run `/ds-audit` to populate component inventory" |
| References fixed | "Review `09b-COMPONENTS.md` for accuracy" |
| Config created from scratch | "Run `/ds-init` to complete project setup" |

### Related Skills

| Skill | When to Suggest |
|-------|-----------------|
| `/ds-init` | If config is severely broken or missing, recommend full re-init |
| `/ds-audit` | After repair, to validate against codebase |
| `/ds-review` | To validate repaired config against actual components |

---

## Example Diagnostic Output

```markdown
## Design System Health Check

**Status**: NEEDS REPAIR
**Config Version**: v2.0 (outdated)
**Last Modified**: 2026-01-15

### Summary

| Category | Status | Issues |
|----------|--------|--------|
| Files | WARN | 1 |
| Schema | ERROR | 2 |
| References | OK | 0 |
| Consistency | INFO | 1 |

### Issues Found

#### CRITICAL
None

#### HIGH
1. **Missing required field**: `stack.state_management` not defined
   - Location: `project-config.yaml`
   - Fix: Add `state_management: none` (or specify jotai/zustand)

2. **Invalid enum value**: `stack.framework: "nextjs"` should be `"next"`
   - Location: `project-config.yaml:5`
   - Fix: Change to valid value `next`

#### MEDIUM
1. **Outdated schema**: Config is v2.0, current is v2.2
   - Location: `project-config.yaml`
   - Fix: Migrate `stack.testing` from string to object

2. **Missing file**: `APPLICATION-DOMAIN/11b-TESTING.md` not found
   - Fix: Create from template

#### LOW
None

#### INFO
1. **Non-standard file**: `APPLICATION-DOMAIN/13b-CUSTOM.md` exists
   - This file is not part of the standard structure
   - May be intentional project extension

### Recommended Actions

1. Run `/ds-repair fix` to auto-repair safe issues
2. Review HIGH severity items - will be prompted during fix
3. Consider schema migration from v2.0 to v2.2
```

---

## Example Repair Output

```markdown
## Design System Repair Report

**Mode**: fix
**Started**: 2026-02-05 10:30:00
**Completed**: 2026-02-05 10:30:15

### Actions Taken

| # | Issue | Action | Result |
|---|-------|--------|--------|
| 1 | Invalid enum `nextjs` | Changed to `next` | FIXED |
| 2 | Missing `state_management` | Added `none` (user confirmed) | FIXED |
| 3 | Outdated schema v2.0 | Migrated to v2.2 (user confirmed) | FIXED |
| 4 | Missing `11b-TESTING.md` | Created from template | FIXED |
| 5 | Non-standard `13b-CUSTOM.md` | Skipped (user choice) | SKIPPED |

### Files Modified

- `.claude/design-system/project-config.yaml` (3 changes)
- `.claude/design-system/APPLICATION-DOMAIN/11b-TESTING.md` (created)

### Post-Repair Status

**Status**: HEALTHY
**Remaining Issues**: None

### Next Steps

1. Review modified files for accuracy
2. Run `/ds-review` to validate components against updated config
3. Commit changes: `git add .claude/design-system/ && git commit -m "fix: repair design system config"`
```

---

## Tool Usage Requirements

**Always use Claude tools, NOT bash commands:**

| Task | Correct Tool | NOT This |
|------|--------------|----------|
| Check file exists | Glob tool | `find`, `ls` |
| Read file content | Read tool | `cat`, `head` |
| Write/create file | Write tool | `echo >`, heredoc |
| Search in files | Grep tool | `grep`, `rg` |
| Modify file | Edit tool | `sed`, `awk` |

**YAML Parsing:**
- Read file content with Read tool
- Parse YAML structure in Claude's context
- Validate against schema definition
- Report errors with line numbers when possible
