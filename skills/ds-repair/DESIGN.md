# ds-repair Skill Design Specification

## Overview

The `ds-repair` skill diagnoses and repairs issues with project-level design system configuration stored in `.claude/design-system/`. It handles corrupted, outdated, or misconfigured files created by `/ds-init`.

---

## 1. Skill Metadata

```yaml
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
```

---

## 2. Expected File Structure

The skill validates and repairs this structure:

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

## 3. Detection Logic

### 3.1 Missing Files Detection

| Check | Method | Severity |
|-------|--------|----------|
| `.claude/design-system/` missing | Glob tool for directory | CRITICAL |
| `project-config.yaml` missing | Read tool attempt | CRITICAL |
| `APPLICATION-DOMAIN/` missing | Glob tool for directory | HIGH |
| Required `.md` files missing | Glob for each file | MEDIUM |

**Detection Steps:**
1. Use Glob tool (NOT find) to check `.claude/design-system/` exists
2. Use Read tool (NOT cat) to attempt reading `project-config.yaml`
3. Use Glob tool to verify `APPLICATION-DOMAIN/` directory
4. Use Glob tool to check each required `.md` file

### 3.2 Invalid Configuration Detection

| Check | Method | Severity |
|-------|--------|----------|
| YAML syntax errors | Parse with error handling | CRITICAL |
| Unknown top-level keys | Compare against schema | MEDIUM |
| Missing required fields | Schema validation | HIGH |
| Wrong value types | Type checking | HIGH |

**Schema Definition:**
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

**Validation Steps:**
1. Attempt YAML parse - catch syntax errors with line numbers
2. Validate top-level keys against schema
3. Check required nested fields exist
4. Validate enum values against allowed options
5. Type-check arrays vs strings vs objects

### 3.3 Outdated Schema Detection

| Check | Method | Severity |
|-------|--------|----------|
| Missing `stack.testing` (v1.x format) | Key absence check | HIGH |
| Using `framework` at root (v1.x) | Key location check | MEDIUM |
| Missing `components.installed` (v1.x) | Key absence check | MEDIUM |

**Version Indicators:**
- v1.x: `framework` at root level, no `stack` object
- v2.0: `stack.framework`, no `stack.testing.unit` split
- v2.2 (current): Full `stack.testing.unit` and `stack.testing.e2e`

### 3.4 Broken References Detection

| Check | Method | Severity |
|-------|--------|----------|
| Components in config but no file | Cross-reference with filesystem | LOW |
| File paths in config that do not exist | Read attempt | MEDIUM |
| Invalid component names (not kebab-case) | Regex validation | LOW |

**Reference Validation:**
1. For each component in `components.installed`, optionally verify `src/components/ui/<name>.tsx` exists
2. For custom components, check documented paths in `09b-COMPONENTS.md`
3. Validate naming conventions (kebab-case for file references)

### 3.5 Inconsistent State Detection

| Check | Method | Severity |
|-------|--------|----------|
| Component in config, file missing | Cross-reference | LOW |
| Component file exists, not in config | Glob + compare | INFO |
| Duplicate entries in arrays | Set comparison | LOW |
| APPLICATION-DOMAIN files reference missing components | Parse + cross-ref | LOW |

---

## 4. Repair Actions

### 4.1 Auto-Fix Actions (No Confirmation Required)

These are safe, non-destructive repairs:

| Issue | Action |
|-------|--------|
| Missing `.claude/design-system/` directory | Create directory |
| Duplicate entries in arrays | Remove duplicates |
| Trailing whitespace in YAML | Trim values |
| Empty arrays where required | Initialize as `[]` |
| Invalid enum values (close match) | Suggest closest valid value |

### 4.2 Prompted Repairs (Require User Confirmation)

| Issue | Prompt | Default Action |
|-------|--------|----------------|
| Missing `project-config.yaml` | "Create from template? (y/n)" | Create with interactive prompts |
| Missing APPLICATION-DOMAIN files | "Create [file] from template? (y/n)" | Create from plugin template |
| Unknown keys in config | "Remove unknown key '[key]'? (y/n)" | Remove |
| Schema migration needed | "Migrate from v[X] to v2.2? (y/n)" | Apply migration |
| Component in config but file missing | "Remove '[component]' from installed list? (y/n)" | Remove reference |

### 4.3 Migration Transformations

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
  ui_library: shadcn
  state_management: none
  testing:
    unit: vitest
    e2e: playwright
```

---

## 5. User Interaction

### 5.1 Interactive Prompts

**Mode Selection:**
```
Design System Repair

Select mode:
1. Check only (diagnose without changes)
2. Auto-fix safe issues + prompt for others
3. Interactive (prompt for every change)

Enter choice [1-3, default=2]:
```

**Repair Confirmation:**
```
Issue: Missing file 'APPLICATION-DOMAIN/09b-COMPONENTS.md'
Action: Create from template with project-specific content

[C]reate from template
[S]kip this issue
[A]bort repair

Choice [C/S/A]:
```

**Migration Prompt:**
```
Schema Migration Required

Current version: v1.x
Target version: v2.2

Changes:
- Move 'framework' into 'stack' object
- Add 'stack.ui_library' (will prompt)
- Add 'stack.state_management' (will prompt)
- Split 'testing' into 'testing.unit' and 'testing.e2e'

Proceed with migration? [Y/n]:
```

### 5.2 Information Gathering (for missing config)

When creating `project-config.yaml` from scratch:

```
Project configuration not found. Let me gather some information:

1. Project name: [auto-detect from package.json or prompt]
2. Project type (application/marketing/hybrid):
3. Framework (next/remix/vite): [auto-detect or prompt]
4. UI library (shadcn/tailwind-ui/both):
5. State management (jotai/zustand/none):
6. Unit testing (vitest/jest): [auto-detect or prompt]
7. E2E testing (playwright/cypress): [auto-detect or prompt]
```

Auto-detection sources:
- `package.json` for name, framework dependencies
- `tsconfig.json` / `vite.config.ts` for framework hints
- `playwright.config.ts` / `jest.config.js` for testing framework

---

## 6. Safety Guardrails

### 6.1 Never Auto-Fix

| Situation | Reason | Action |
|-----------|--------|--------|
| YAML with unrecoverable syntax errors | Cannot parse structure | Abort, show error location |
| Custom fields that might be intentional | Could be user extensions | Prompt before removal |
| Files with uncommitted git changes | Could lose user work | Warn and prompt |
| Non-standard APPLICATION-DOMAIN names | Could be intentional (MARKETING-DOMAIN) | Skip, report as info |

### 6.2 Abort Conditions

Immediately abort repair and report:

1. **No `.claude/` directory** - Project may not use Claude Code
2. **Binary files detected** in design-system directory
3. **Circular references** in YAML (anchors/aliases causing loops)
4. **File permissions error** - Cannot write to `.claude/design-system/`
5. **Disk full** - Insufficient space for repairs

### 6.3 Backup Strategy

Before any file modification:

1. Check if git is initialized and file is tracked
2. If uncommitted changes exist, warn user: "Uncommitted changes in [file]. Recommend committing first."
3. For non-git projects, create `.claude/design-system/.backup/` with timestamped copies

---

## 7. Output Format

### 7.1 Diagnostic Report

```markdown
## Design System Health Check

**Status**: [HEALTHY | NEEDS REPAIR | CRITICAL]
**Config Version**: v2.2 (current) | v2.0 (outdated) | v1.x (legacy)
**Last Modified**: 2026-02-01

### Summary

| Category | Status | Issues |
|----------|--------|--------|
| Files | OK | 0 |
| Schema | WARN | 2 |
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
None

#### LOW
1. **Orphaned file**: `APPLICATION-DOMAIN/13b-CUSTOM.md` not referenced
   - Location: `.claude/design-system/APPLICATION-DOMAIN/`
   - Info: File exists but is non-standard. May be intentional.

### Recommended Actions

1. Run `/ds-repair fix` to auto-repair safe issues
2. Review HIGH severity items before proceeding
3. Consider running `/ds-init` if config is severely broken
```

### 7.2 Repair Report

```markdown
## Design System Repair Report

**Mode**: Auto-fix + prompted
**Started**: 2026-02-05 10:30:00
**Completed**: 2026-02-05 10:30:15

### Actions Taken

| # | Issue | Action | Result |
|---|-------|--------|--------|
| 1 | Invalid enum `nextjs` | Changed to `next` | FIXED |
| 2 | Missing `state_management` | Added `none` | FIXED |
| 3 | Missing `09b-COMPONENTS.md` | Created from template | FIXED |
| 4 | Orphaned `13b-CUSTOM.md` | Skipped (user choice) | SKIPPED |

### Files Modified

- `.claude/design-system/project-config.yaml` (2 changes)
- `.claude/design-system/APPLICATION-DOMAIN/09b-COMPONENTS.md` (created)

### Post-Repair Status

**Status**: HEALTHY
**All required files**: Present
**Schema version**: v2.2 (current)

### Next Steps

1. Review modified files for accuracy
2. Run `/ds-review` to validate against design system
3. Commit changes: `git add .claude/design-system/ && git commit -m "fix: repair design system config"`
```

---

## 8. Integration Points

### 8.1 Related Skills

| Skill | Integration |
|-------|-------------|
| `/ds-init` | Fallback for severely broken config - suggest full re-init |
| `/ds-audit` | Run audit after repair to validate against codebase |
| `/ds-review` | Validate repaired config against actual components |

### 8.2 Skill Invocation Suggestions

After repair, suggest next steps based on what was fixed:

- If schema migrated: "Run `/ds-review` to ensure components match updated config"
- If files recreated: "Run `/ds-audit` to populate component inventory"
- If references fixed: "Review `09b-COMPONENTS.md` for accuracy"

---

## 9. Error Messages

### 9.1 User-Friendly Error Messages

| Error | Message |
|-------|---------|
| YAML syntax error | "Config file has invalid YAML syntax at line [N]. Error: [message]" |
| Permission denied | "Cannot write to `.claude/design-system/`. Check file permissions." |
| Unknown framework | "Framework '[value]' not recognized. Valid options: next, remix, vite" |
| Missing directory | "Design system not initialized. Run `/ds-init` first." |

### 9.2 Recovery Suggestions

Always provide actionable recovery steps:

```
Error: YAML syntax error at line 12

The project-config.yaml file has invalid syntax:
  Expected ':' but found '}'

Options:
1. Fix manually: Open .claude/design-system/project-config.yaml and correct line 12
2. Reset config: Run `/ds-init` to recreate from scratch (will prompt for values)
3. Restore from git: Run `git checkout .claude/design-system/project-config.yaml`
```

---

## 10. Implementation Notes

### 10.1 Tool Usage Requirements

**Claude executes** - Use Read tool (NOT cat) for all file reads
**Claude executes** - Use Write tool (NOT heredoc/bash) for all file writes
**Claude executes** - Use Glob tool (NOT find) for file discovery
**Claude executes** - Use Grep tool (NOT grep command) for pattern matching

### 10.2 YAML Parsing Strategy

1. Read file content with Read tool
2. Parse YAML in Claude's context (not via bash yaml tools)
3. Validate against schema definition
4. Report errors with line numbers when possible

### 10.3 Template Resolution

Templates are sourced from:
```
${CLAUDE_PLUGIN_ROOT}/templates/project-config.yaml
${CLAUDE_PLUGIN_ROOT}/templates/APPLICATION-DOMAIN/*.md
```

Where `CLAUDE_PLUGIN_ROOT` is typically `~/.claude/plugins/design-system` or the plugin installation path.

---

## 11. Test Scenarios

### 11.1 Test Cases

| Scenario | Input State | Expected Outcome |
|----------|-------------|------------------|
| Healthy config | All files present, valid YAML | Status: HEALTHY, no actions |
| Missing directory | No `.claude/design-system/` | Prompt for init or create |
| YAML syntax error | Invalid YAML in config | Report error location, suggest fix |
| Legacy v1.x config | Old schema format | Offer migration to v2.2 |
| Missing component file | Component listed but no file | Offer to remove from list |
| Extra custom domain | MARKETING-DOMAIN exists | Report as INFO, do not modify |

### 11.2 Edge Cases

1. **Empty config file**: Treat as missing, offer to recreate
2. **Config with only comments**: Treat as missing, offer to recreate
3. **Binary data in YAML file**: Abort with clear error
4. **Extremely large config (>100KB)**: Warn about unusual size, proceed carefully
5. **Symlinked files**: Follow symlinks but warn user

---

## 12. Future Considerations

### 12.1 Potential Enhancements

- **Config diffing**: Show what changed between versions
- **Remote template sync**: Pull latest templates from plugin repo
- **Multi-domain support**: Handle projects with multiple APPLICATION-DOMAIN variants
- **Config locking**: Prevent accidental modifications with checksum

### 12.2 Compatibility Notes

- Skill designed for design-system plugin v2.2.0+
- Backward compatible with v1.x and v2.0 configs (via migration)
- Forward compatible with future versions (unknown keys preserved unless explicitly removed)
