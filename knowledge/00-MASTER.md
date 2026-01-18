---
title: "AI Design System: Master Document"
version: "2.1"
audience: ["all"]
dependencies: []
last_updated: "2026-01-18"
---

# AI Design System: Master Document

**Version:** 2.1  
**Status:** Production-Ready  
**Last Updated:** January 2026  
**Primary Audience:** AI/LLM agents and the teams maintaining them

---

## WELCOME

You're reading the master document for a **complete, production-ready design system** that covers:
- ✅ Marketing sites (using Tailwind UI)
- ✅ Full SPA applications (using shadcn/ui)
- ✅ Design tokens (colors, typography, spacing, motion)
- ✅ Component specifications
- ✅ Accessibility standards (WCAG AA)
- ✅ State management patterns
- ✅ Testing strategies
- ✅ Implementation guides

This system is **modular, scalable, and technology-independent at its core**—meaning the foundational design decisions survive even if you change tools later.

---

## QUICK START: FIND YOUR PATH

### I'm an **LLM Agent** implementing this system
**Read this:** 10 minutes  
1. [LLM-AGENT-GUIDE.md](./LLM-AGENT-GUIDE.md) — Retrieval order and output rules  
2. [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) — Visual source of truth  
3. [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) — Component intent and usage  
4. [LAYER-07b-PROMPT-PATTERNS.md](./LAYER-07b-PROMPT-PATTERNS.md) — Prompt UI patterns  
5. [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) — Accessibility + reduced motion  

**Then choose your domain:**
- **Marketing site?** → [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md)  
- **Application?** → [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md)

### I'm a **New Developer** joining the team
**Read this:** 30 minutes  
1. This page (Master Document)
2. [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md) — Understand WHY
3. [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) — Learn the building blocks

**Then choose your domain:**
- **Building the marketing site?** → [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md)
- **Building the app?** → [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md)

---

### I'm a **Designer** working on components
**Read this:** 20 minutes  
1. [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) — All our colors, typography, spacing
2. [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) — Component specs and visual guidelines
3. [LAYER-03-LAYOUT-PATTERNS.md](./LAYER-03-LAYOUT-PATTERNS.md) — Responsive architecture

**Then:** Check [APPENDIX-B-COMPONENT-DECISIONS.md](./APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md) to decide what component to build.

---

### I'm an **Architect** making system decisions
**Read this:** 45 minutes  
1. [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md) — Why we chose Tailwind + shadcn/ui
2. [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md) — How styling is organized
3. [LAYER-06-STATE-MANAGEMENT.md](./LAYER-06-STATE-MANAGEMENT.md) — Application architecture
4. [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) — Our standards

---

### I'm a **Frontend Developer** building components
**Read this:** 60 minutes  
1. [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) — Tokens you'll use
2. [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) — Component APIs
3. [LAYER-05-RESPONSIVE-DESIGN.md](./LAYER-05-RESPONSIVE-DESIGN.md) — Responsive patterns
4. [LAYER-07-INTERACTION-PATTERNS.md](./LAYER-07-INTERACTION-PATTERNS.md) — User experience patterns
5. [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) — Accessibility requirements

---

### I'm a **Product Manager** reviewing the system
**Read this:** 25 minutes  
1. [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md) — High-level overview
2. [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) — What we can build
3. [LAYER-07-INTERACTION-PATTERNS.md](./LAYER-07-INTERACTION-PATTERNS.md) — User experience patterns

---

### I need to **troubleshoot something**
Go directly to: [APPENDIX-D-TROUBLESHOOTING.md](./APPENDICES/APPENDIX-D-TROUBLESHOOTING.md)

---

## SYSTEM ARCHITECTURE AT A GLANCE

```
┌─────────────────────────────────────────────────────────────────┐
│  SHARED FOUNDATION (Technology-Independent)                     │
│  Layers 0-5: Strategic decisions, tokens, components, layout    │
│  Layers 6-8: State management, interactions, accessibility      │
│  → These layers never change, even if tools do                  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
         ┌────────────────────┴────────────────────┐
         ↓                                         ↓
    MARKETING DOMAIN                      APPLICATION DOMAIN
    (Tailwind UI)                          (shadcn/ui)
    Layers 9a-12a                          Layers 9b-12b
    ├─ Hero templates                      ├─ 50+ components
    ├─ Feature blocks                      ├─ Custom components
    ├─ Pricing tables                      ├─ Full app patterns
    └─ Marketing flows                     └─ Testing strategy
```

**Key insight:** Both domains use the SAME tokens and patterns from the foundation. This ensures consistency across your entire product.

---

## FILE STRUCTURE

### 📚 FOUNDATIONS/ (Technology-Independent Core)
These layers describe WHAT we need to build, not HOW we build it.

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md) | Why Tailwind + shadcn/ui? Why headless? Cost analysis. | Everyone | 15 min |
| [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) | Complete token system: colors, typography, spacing, shadows, motion, z-index | Designers, Developers | 20 min |
| [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) | What components exist, their props, accessibility notes, usage examples | Designers, Developers | 25 min |
| [LAYER-03-LAYOUT-PATTERNS.md](./LAYER-03-LAYOUT-PATTERNS.md) | Layout systems, grid patterns, responsive architecture, mobile vs. desktop | Designers, Developers | 15 min |
| [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md) | How styling is organized, Tailwind config, naming conventions, file structure | Developers | 20 min |
| [LAYER-05-RESPONSIVE-DESIGN.md](./LAYER-05-RESPONSIVE-DESIGN.md) | Breakpoints, mobile-first philosophy, per-component behavior at different sizes | Developers, Designers | 15 min |

### 🎯 PATTERNS/ (Used Everywhere)
These patterns apply to both marketing and application domains.

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [LAYER-06-STATE-MANAGEMENT.md](./LAYER-06-STATE-MANAGEMENT.md) | How components and apps manage state; Jotai, Server Components | Developers | 20 min |
| [LAYER-07-INTERACTION-PATTERNS.md](./LAYER-07-INTERACTION-PATTERNS.md) | Loading states, error states, success states, timing, animations | Designers, Developers | 20 min |
| [LAYER-07b-PROMPT-PATTERNS.md](./LAYER-07b-PROMPT-PATTERNS.md) | Prompt UI patterns, suggestions, regeneration | Designers, Developers, LLMs | 15 min |
| [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) | WCAG AA standards, color contrast, keyboard navigation, ARIA attributes | Everyone | 25 min |

### 🌐 MARKETING-DOMAIN/
Tailwind UI templates for marketing sites.

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md) | Canonical guide for templates, setup, and implementation | Developers | 30 min |

**Entry points:** [09a-TEMPLATES.md](./MARKETING-DOMAIN/09a-TEMPLATES.md), [10a-SETUP.md](./MARKETING-DOMAIN/10a-SETUP.md), [12a-IMPLEMENTATION.md](./MARKETING-DOMAIN/12a-IMPLEMENTATION.md)

### 💻 APPLICATION-DOMAIN/
shadcn/ui components for full SPA applications.

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md) | Canonical guide for components, setup, testing, implementation | Developers | 30 min |

**Entry points:** [09b-COMPONENTS.md](./APPLICATION-DOMAIN/09b-COMPONENTS.md), [10b-SETUP.md](./APPLICATION-DOMAIN/10b-SETUP.md), [11b-TESTING.md](./APPLICATION-DOMAIN/11b-TESTING.md), [12b-IMPLEMENTATION.md](./APPLICATION-DOMAIN/12b-IMPLEMENTATION.md)

### 📖 APPENDICES/
Reference material and decision guides.

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [APPENDIX-A-TAILWIND-UTILITIES.md](./APPENDICES/APPENDIX-A-TAILWIND-UTILITIES.md) | Complete Tailwind CSS utility reference | Developers | 40 min |
| [APPENDIX-B-COMPONENT-DECISIONS.md](./APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md) | Decision trees: "Button or Link?" "Dialog or Drawer?" | Developers | 15 min |
| [APPENDIX-C-RADIX-PRIMITIVES.md](./APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md) | Radix UI primitives that power shadcn/ui | Developers | 20 min |
| [APPENDIX-D-TROUBLESHOOTING.md](./APPENDICES/APPENDIX-D-TROUBLESHOOTING.md) | Common issues, solutions, FAQs | Everyone | 20 min |
| [APPENDIX-E-MIGRATION-PATHS.md](./APPENDICES/APPENDIX-E-MIGRATION-PATHS.md) | Migration strategies for the future | Architects | 15 min |

### 🤖 LLM AGENT GUIDE

| File | Purpose | Audience | Read Time |
|------|---------|----------|-----------|
| [LLM-AGENT-GUIDE.md](./LLM-AGENT-GUIDE.md) | Retrieval order, canonical files, output rules | LLMs, Maintainers | 10 min |

---

## KEY PRINCIPLES

### 1. **Foundation First**
Every design decision is grounded in tokens (Layer 1). Change a token, and all components using it update automatically. No scattered color values.

### 2. **Technology-Independent Core**
Layers 0-8 describe WHAT you need. Layers 9-12 describe HOW to implement it. If you switch from Tailwind to CSS Modules, only Layer 4 changes.

### 3. **Modular Architecture**
Each layer is a separate file with a single responsibility. Update Layer 1 tokens without touching Layer 2 components. Update Layer 9b components without affecting Layer 9a templates.

### 4. **Shared Patterns Everywhere**
Layers 6-8 (state management, interactions, accessibility) plus Layer 7b (prompt patterns) apply to both marketing and application. Everything is consistent.

### 5. **Code Ownership**
Both Tailwind UI and shadcn/ui give you code ownership. You copy the code into your repo. No mysterious updates or vendor lock-in.

### 6. **Scalable**
Works for solo developer building a marketing site. Works for 5-person team building a full SPA. Works for 20+ person organization with multiple products.

---

## QUICK REFERENCE: WHEN TO USE WHAT

| Question | Answer | Location |
|----------|--------|----------|
| **What colors do we have?** | See the complete color palette | [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) |
| **What spacing scale do we use?** | 4px base unit, scales to 4, 8, 12, 16, 20, 24, 32, etc. | [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md) |
| **Do we have a Button component?** | Yes, with 5 variants (primary, secondary, outline, ghost, link) | [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md) |
| **How do I handle loading states?** | Skeleton, spinner, progress bar, or toast depending on context | [LAYER-07-INTERACTION-PATTERNS.md](./LAYER-07-INTERACTION-PATTERNS.md) |
| **How do we render prompts and regeneration?** | Prompt UI patterns and actions | [LAYER-07b-PROMPT-PATTERNS.md](./LAYER-07b-PROMPT-PATTERNS.md) |
| **What's the color contrast requirement?** | WCAG AA: 4.5:1 for normal text, 3:1 for large text | [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) |
| **How do I set up the marketing site?** | Step-by-step guide + code examples | [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md) |
| **How do I set up the app?** | Step-by-step guide + code examples | [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md) |
| **I need to choose between Button and Link** | Decision tree | [APPENDIX-B-COMPONENT-DECISIONS.md](./APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md) |
| **Something's broken** | Troubleshooting guide | [APPENDIX-D-TROUBLESHOOTING.md](./APPENDICES/APPENDIX-D-TROUBLESHOOTING.md) |

---

## HOW THIS SYSTEM SCALES

### Solo Developer (You, right now)
- ✅ Read Layers 0-5 to understand the system
- ✅ Pick your domain (marketing or app)
- ✅ Build using provided implementation guides
- ✅ Reference appendices as needed
- **Time to productivity:** 2-3 days

### 3-Person Team
- ✅ One person reads entire system (2-3 days)
- ✅ Shares key docs with others (Layers 0, 1, 2 mandatory)
- ✅ Pair program on first few components
- ✅ Establish code review process
- **Time to productivity:** 1 week

### 5-Person Team
- ✅ Assign ownership: Designer owns Layer 1-2-3, Backend dev owns Layer 6, Frontend lead owns Layers 4-5
- ✅ Weekly sync on design system evolution
- ✅ Use appendices for decision-making
- ✅ Maintain token docs as single source of truth
- **Time to productivity:** 2 weeks

### 10+ Person Organization
- ✅ Designate Design Systems Lead
- ✅ Quarterly reviews of each layer
- ✅ Create feature branches for system updates
- ✅ Document any additions to component library
- ✅ Version the design system (v1.0, v1.1, v2.0)
- **Time to productivity:** 1 month

---

## UPDATING THIS SYSTEM

When you need to add something new:

1. **New token (color, spacing, etc.)?** → Update [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md)
2. **New component type?** → Update [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md)
3. **New CSS strategy?** → Update [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md)
4. **New state pattern?** → Update [LAYER-06-STATE-MANAGEMENT.md](./LAYER-06-STATE-MANAGEMENT.md)
5. **New marketing template?** → Update [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md)
6. **New app component?** → Update [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md)
7. **New prompt pattern?** → Update [LAYER-07b-PROMPT-PATTERNS.md](./LAYER-07b-PROMPT-PATTERNS.md)

**Each update is a single file change.** No need to touch the entire system.

---

## WHAT MAKES THIS DIFFERENT

### Compared to MUI-based systems:
- ❌ MUI: "Use MUI Joy UI and customize it"
- ✅ This: "Here are our tokens and patterns; we implement with Tailwind + shadcn/ui"

### Compared to monolithic design system docs:
- ❌ 60-page PDF: Hard to find things, hard to update
- ✅ This: 15 canonical files + entry points, easy to navigate, easy to update

### Compared to scattered docs:
- ❌ "Design tokens in Figma, components in Storybook, patterns in Notion"
- ✅ This: Everything in one place, version controlled, searchable

---

## NEXT STEPS

1. **Read [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md)** to understand WHY we made these choices
2. **Read [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md)** to see the complete token system
3. **Pick your domain** (marketing or app) and jump to the implementation guide
4. **Bookmark [APPENDIX-D-TROUBLESHOOTING.md](./APPENDICES/APPENDIX-D-TROUBLESHOOTING.md)** for when you have questions

---

## QUESTIONS?

- **"Why Tailwind CSS?"** → [LAYER-00-STRATEGIC-DECISIONS.md](./LAYER-00-STRATEGIC-DECISIONS.md)
- **"What colors do we have?"** → [LAYER-01-DESIGN-TOKENS.md](./LAYER-01-DESIGN-TOKENS.md)
- **"How do I build a component?"** → [LAYER-02-COMPONENTS.md](./LAYER-02-COMPONENTS.md)
- **"How should this respond on mobile?"** → [LAYER-05-RESPONSIVE-DESIGN.md](./LAYER-05-RESPONSIVE-DESIGN.md)
- **"What's our state management strategy?"** → [LAYER-06-STATE-MANAGEMENT.md](./LAYER-06-STATE-MANAGEMENT.md)
- **"How do prompts and regeneration work?"** → [LAYER-07b-PROMPT-PATTERNS.md](./LAYER-07b-PROMPT-PATTERNS.md)
- **"How do I make this accessible?"** → [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md)
- **"I'm an LLM agent, where do I start?"** → [LLM-AGENT-GUIDE.md](./LLM-AGENT-GUIDE.md)
- **"How do I build the marketing site?"** → [MARKETING-DOMAIN-guide.md](./MARKETING-DOMAIN-guide.md)
- **"How do I build the app?"** → [APPLICATION-DOMAIN-guide.md](./APPLICATION-DOMAIN-guide.md)
- **"Something's broken"** → [APPENDIX-D-TROUBLESHOOTING.md](./APPENDICES/APPENDIX-D-TROUBLESHOOTING.md)

---

**Last Updated:** January 18, 2026  
**Status:** Production-Ready  
**Version:** 2.1

Start with the layer that matters most to you. Everything else is a click away.
