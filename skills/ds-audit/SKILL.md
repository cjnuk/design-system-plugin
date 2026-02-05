---
skill: ds-audit
description: Audit existing codebase against design system standards and produce migration recommendations
triggers:
  - "ds audit"
  - "audit design system"
  - "review against design system"
  - "design system compliance"
  - "check design system"
---

# Design System Audit

Comprehensive audit of an existing codebase against design system standards.

## Context Resolution

Before auditing, check for project-specific configuration:

1. Read `.claude/design-system/project-config.yaml` for project metadata
2. Read `.claude/design-system/APPLICATION-DOMAIN/*.md` for existing project patterns
3. Reference `${CLAUDE_PLUGIN_ROOT}/system/LAYER-*.md` for design system standards

## Audit Process

### Phase 1: Discovery (Quick Triage)

**Step 1: Project Structure Analysis**
- Identify component directories (src/components/, components/, ui/)
- Count total components and categorize by type
- Identify styling approach (Tailwind, CSS Modules, styled-components)

**Step 2: Quick Scan**
Use Glob and Grep to identify:
- Components: `**/*.tsx`, `**/*.jsx` files in component directories
- Styles: Hardcoded colors (#hex, rgb), spacing (px values), typography
- Patterns: Form handling, state management, loading states

### Phase 2: Gap Analysis

**Component Inventory**
Compare found components against LAYER-02-COMPONENTS.md:

| Status | Meaning |
|--------|---------|
| ✅ Aligned | Component matches design system |
| ⚠️ Partial | Similar but needs adjustment |
| ❌ Missing | Design system component not used |
| 🆕 Custom | App-specific, consider for APPLICATION-DOMAIN |

**Token Analysis**
Compare against LAYER-01-DESIGN-TOKENS.md:

| Category | What to Check |
|----------|---------------|
| Colors | Hardcoded hex/rgb vs semantic tokens |
| Spacing | px/rem values vs spacing scale |
| Typography | Font sizes, weights, families |
| Shadows | Box-shadow values vs elevation tokens |
| Z-index | Arbitrary values vs z-index scale |

**Pattern Analysis**
Compare against LAYER-07, LAYER-07c, LAYER-08:

| Pattern | Standard | Check For |
|---------|----------|-----------|
| Loading states | 150-300ms delay | Immediate spinners |
| Error handling | Toast + inline | Alert boxes |
| Accessibility | WCAG AA, APCA | Missing ARIA, poor contrast |
| Animation | GPU-accelerated | Layout-triggering animations |
| Forms | Label every input | Placeholder-only inputs |

### Phase 3: Report Generation

Generate audit report with sections:

1. **Executive Summary**
   - Overall compliance score (0-100%)
   - Critical issues count
   - Estimated migration effort (S/M/L)

2. **Component Inventory**
   - Table of all components with status
   - Recommended actions per component

3. **Token Violations**
   - List of hardcoded values with file:line
   - Suggested token replacements

4. **Pattern Gaps**
   - Interaction pattern issues
   - Accessibility concerns
   - Animation/motion issues

5. **Migration Recommendations**
   - Prioritized list (Quick Wins → Critical Path)
   - Suggested approach per area (Adopt/Adapt/Rebuild)
   - Estimated effort per item

6. **APPLICATION-DOMAIN Candidates**
   - Patterns worth preserving
   - Suggested additions to project config

## Output Format

Save report to: `.claude/design-system/AUDIT-REPORT.md`

Also scaffold project config if not exists:
- `.claude/design-system/project-config.yaml`
- `.claude/design-system/APPLICATION-DOMAIN/` directory

## Example Usage

User: "Audit this project against the design system"

Claude:
1. Scans project structure
2. Analyzes components, tokens, patterns
3. Generates comprehensive audit report
4. Scaffolds project config if needed
5. Provides prioritized migration recommendations

## Quick Audit vs Full Audit

**Quick Audit** (default):
- 5-10 minute scan
- Top-level findings
- Use for initial assessment

**Full Audit** (request with "full audit" or "comprehensive audit"):
- 30-60 minute deep analysis
- Line-by-line token violations
- Complete component mapping
- Use before major migration

## Integration with Other Skills

After audit, use:
- `/ds-init` - If project config doesn't exist
- `/ds-migrate` - To plan migration based on audit
- `/ds-component` - To create compliant components
- `/ds-lint-setup` - To automate standard enforcement

## References

- [LAYER-01: Design Tokens](../../system/LAYER-01-DESIGN-TOKENS.md)
- [LAYER-02: Components](../../system/LAYER-02-COMPONENTS.md)
- [LAYER-07: Interaction Patterns](../../system/LAYER-07-INTERACTION-PATTERNS.md)
- [LAYER-08: Accessibility](../../system/LAYER-08-ACCESSIBILITY.md)
