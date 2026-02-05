---
skill: design-system
description: Design system assistance - routes to specialized agents based on request type
triggers:
  - "design system"
  - "ds help"
  - "help with ui"
  - "shadcn help"
  - "ui assistance"
invoke_agent: router-agent
---

# Design System Router

Dispatches to router-agent for intelligent request routing.

The router agent will:
1. Load LLM-AGENT-GUIDE.md and 00-MASTER.md for routing decisions
2. Analyze the user's request to determine intent
3. Route to the appropriate specialized agent:
   - component-agent: Component generation
   - form-agent: Form field patterns
   - accessibility-agent: A11y fixes
   - theming-agent: Dark mode + tokens
   - review-agent: Code review
   - migration-agent: Framework migration
   - setup-agent: Project setup
4. Provide context to the dispatched agent
