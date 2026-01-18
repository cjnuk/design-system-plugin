---
title: "File Manifest"
version: "2.1"
last_updated: "2026-01-18"
---

# DESIGN SYSTEM DOCUMENTATION: FILE MANIFEST

**Generated:** January 2026  
**Status:** Complete  
**Version:** 2.1

---

## CANONICAL SPEC FILES (SOURCE OF TRUTH)

### Master & Strategy
- **00-MASTER.md** — Entry point, navigation guides, quick reference
- **LAYER-00-STRATEGIC-DECISIONS.md** — Why we chose Tailwind v4 + shadcn/ui v2
- **LLM-AGENT-GUIDE.md** — Task-specific retrieval paths, output rules

### Foundations & Patterns (Layers 1-8)
- **LAYER-01-DESIGN-TOKENS.md** — Token system (colors, typography, spacing, shadows, motion)
- **LAYER-02-COMPONENTS.md** — Component inventory, usage, accessibility
- **LAYER-03-LAYOUT-PATTERNS.md** — Layout systems, responsive architecture
- **LAYER-04-CSS-ARCHITECTURE.md** — Tailwind organization, naming, optimization
- **LAYER-05-RESPONSIVE-DESIGN.md** — Breakpoints, mobile-first behavior
- **LAYER-06-STATE-MANAGEMENT.md** — State patterns (Jotai, Server Components)
- **LAYER-07-INTERACTION-PATTERNS.md** — Loading, error, success states
- **LAYER-07b-PROMPT-PATTERNS.md** — Prompt UI, suggestions, regeneration
- **LAYER-08-ACCESSIBILITY.md** — WCAG 2.1 AA + reduced motion
- **LAYER-09-CI-AUTOMATION.md** — CI/CD, Storybook, TypeDoc, link checking

### Domain Guides (with Standalone Files)

**MARKETING-DOMAIN/**
- **MARKETING-DOMAIN-guide.md** — Overview and navigation
- **MARKETING-DOMAIN/09a-TEMPLATES.md** — Tailwind UI template catalog
- **MARKETING-DOMAIN/10a-SETUP.md** — Project setup instructions
- **MARKETING-DOMAIN/12a-IMPLEMENTATION.md** — Implementation patterns

**APPLICATION-DOMAIN/**
- **APPLICATION-DOMAIN-guide.md** — Overview and navigation
- **APPLICATION-DOMAIN/09b-COMPONENTS.md** — shadcn/ui component catalog
- **APPLICATION-DOMAIN/10b-SETUP.md** — Project setup instructions
- **APPLICATION-DOMAIN/11b-TESTING.md** — Testing strategy (Vitest, Playwright)
- **APPLICATION-DOMAIN/12b-IMPLEMENTATION.md** — Implementation patterns

### Appendices (Standalone Files)
- **APPENDICES/APPENDIX-A-TAILWIND-UTILITIES.md** — Utility class reference
- **APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md** — Component ADRs
- **APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md** — Radix primitive mappings
- **APPENDICES/APPENDIX-D-TROUBLESHOOTING.md** — Common issues and fixes
- **APPENDICES/APPENDIX-E-MIGRATION-PATHS.md** — Framework migration guides

### Prompts Library
- **PROMPTS/01-NEW-COMPONENT.md** — Create new component
- **PROMPTS/02-FORM-FIELD.md** — Add form field with validation
- **PROMPTS/03-FIX-ACCESSIBILITY.md** — Fix accessibility issue
- **PROMPTS/04-DARK-MODE.md** — Implement dark mode
- **PROMPTS/05-CODE-REVIEW.md** — Code review checklist
- **PROMPTS/06-MIGRATION.md** — Migrate from other frameworks

---

## META / STATUS FILES

- **_FILE-MANIFEST.md** — This file
- **CHANGELOG.md** — Version history

---

## CI/TOOLING FILES

- **.github/workflows/design-system.yml** — GitHub Actions workflow
- **.markdownlint.json** — Markdown linting config

---

## UPDATE POLICY

1. All new files must include YAML front-matter (title, version, audience, dependencies, last_updated)
2. Update this manifest when adding/removing files
3. For LLM usage, follow LLM-AGENT-GUIDE.md task-specific retrieval paths
4. For component props, prefer generated docs (TypeDoc/Storybook) when available
