---
skill: ds-component
description: Generate a new UI component following design system patterns with cva, TypeScript types, and accessibility
triggers:
  - "create component"
  - "new component"
  - "generate component"
  - "ds component"
  - "add a component"
arguments:
  - name: component_name
    description: Name of the component to create (e.g., Badge, StatusIndicator)
    required: true
invoke_agent: component-agent
---

# Create New Component

Dispatches to component-agent for generating UI components.

The agent will:
1. Load PROMPTS/01-NEW-COMPONENT.md and LAYER-02-COMPONENTS.md
2. Analyze component requirements and variants needed
3. Generate shadcn/ui-style component with cva
4. Ensure semantic token usage (no hardcoded colors)
5. Add TypeScript types with VariantProps
6. Include accessibility attributes
7. Validate against design system rules
