---
skill: ds-review
description: Review code against design system standards for tokens, accessibility, TypeScript, and patterns
triggers:
  - "review code"
  - "code review"
  - "ds review"
  - "check design system"
  - "validate against design system"
arguments:
  - name: file_path
    description: Path to the file or component to review
    required: false
invoke_agent: review-agent
---

# Design System Code Review

Dispatches to review-agent for design system compliance review.

The agent will:
1. Load PROMPTS/05-CODE-REVIEW.md and LLM-AGENT-GUIDE.md
2. Check semantic token usage (no hardcoded colors)
3. Verify cva patterns for variants
4. Validate TypeScript types (no any)
5. Audit accessibility compliance
6. Review naming conventions
7. Provide actionable fixes with code diffs
