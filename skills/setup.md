---
skill: ds-setup
description: Initialize a project with design system tokens, dependencies, and shadcn/ui configuration
triggers:
  - "set up design system"
  - "initialize design system"
  - "ds setup"
  - "configure shadcn"
  - "install design system"
invoke_agent: setup-agent
---

# Design System Setup

Dispatches to setup-agent for initializing projects with the design system.

The agent will:
1. Load LAYER-00-STRATEGIC-DECISIONS.md for setup guidance
2. Install required dependencies (shadcn/ui, cva, clsx, tailwind-merge)
3. Configure Tailwind CSS with semantic tokens
4. Set up the cn() utility function
5. Initialize shadcn/ui with proper theming
6. Create CSS custom properties for light/dark mode
7. Validate the setup against design system rules
