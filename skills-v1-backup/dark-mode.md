---
skill: ds-dark-mode
description: Implement dark mode with CSS custom properties and system preference detection
triggers:
  - "add dark mode"
  - "implement dark mode"
  - "ds dark mode"
  - "theme toggle"
  - "dark theme"
invoke_agent: theming-agent
---

# Implement Dark Mode

Dispatches to theming-agent for dark mode implementation.

The agent will:
1. Load PROMPTS/04-DARK-MODE.md and LAYER-01-DESIGN-TOKENS.md
2. Set up CSS custom properties for light/dark themes
3. Configure next-themes provider
4. Create accessible theme toggle component
5. Ensure all colors use semantic tokens
6. Validate contrast ratios in both modes
