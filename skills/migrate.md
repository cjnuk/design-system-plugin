---
skill: ds-migrate
description: Migrate components from Material UI, Chakra UI, or Bootstrap to shadcn/ui
triggers:
  - "migrate component"
  - "convert to shadcn"
  - "ds migrate"
  - "migrate from mui"
  - "migrate from chakra"
  - "migrate from bootstrap"
arguments:
  - name: source_framework
    description: Source framework (mui, chakra, bootstrap)
    required: false
  - name: component
    description: Component to migrate
    required: false
invoke_agent: migration-agent
---

# Migrate to shadcn/ui

Dispatches to migration-agent for framework migration.

The agent will:
1. Load PROMPTS/06-MIGRATION.md and APPENDIX-E-MIGRATION-PATHS.md
2. Identify source framework component patterns
3. Map to shadcn/ui equivalents
4. Convert props and variants
5. Replace hardcoded colors with semantic tokens
6. Update dependencies
7. Validate migrated code against design system rules
