---
title: "Prompt: Generate Stack Constraints"
task_type: "constraint-generation"
retrieval_path: ["LAYER-00-STRATEGIC-DECISIONS"]
---

# PROMPT: GENERATE STACK CONSTRAINTS

Use this prompt to generate a constraint string that can be prepended to any `frontend-design` invocation, ensuring generated UI code adheres to this design system's tech stack.

---

## PROMPT TEMPLATE

```
Read system/LAYER-00-STRATEGIC-DECISIONS.md and extract a concise stack constraint block.

Your output must follow this exact structure:

MANDATORY STACK CONSTRAINTS (from design system):

ALLOWED:
- [List each approved technology from "What We Chose" section]
- [Include framework, CSS approach, component library, state management, testing tools]
- [Include React version and component style if specified]

FORBIDDEN:
- [List each rejected option from "What We Rejected" section]
- [Add any patterns explicitly discouraged in the document]
- [Include: other frameworks (Vue, Svelte, Angular), rejected component libraries, rejected CSS approaches]
- [Include: class components, raw HTML elements where design system components exist]

SOURCE: system/LAYER-00-STRATEGIC-DECISIONS.md

You MUST output only code using the ALLOWED technologies. Never use FORBIDDEN technologies.

---

Rules for extraction:
1. Be concise — each item should be a short phrase, not a sentence
2. Include version numbers where specified
3. For FORBIDDEN, include both explicitly rejected items AND logical exclusions (e.g., if React is allowed, Vue/Svelte/Angular are forbidden)
4. The final line "You MUST output only..." should reference the primary framework and styling approach
```

---

## EXPECTED OUTPUT FORMAT

The generated constraint block should look like:

```
MANDATORY STACK CONSTRAINTS (from design system):

ALLOWED:
- React 19 (functional components, hooks, Server Components)
- Tailwind CSS v4 (utility-first)
- shadcn/ui v2 components (built on Radix UI primitives)
- Jotai for atomic state
- Vitest + React Testing Library + Playwright
- TypeScript

FORBIDDEN:
- Vue, Svelte, Angular, or any non-React framework
- MUI, Chakra UI, Bootstrap, or other component libraries
- CSS-in-JS (styled-components, Emotion)
- Class components
- Raw HTML elements where a shadcn/ui component exists
- Hardcoded colors or spacing (use semantic tokens)

SOURCE: system/LAYER-00-STRATEGIC-DECISIONS.md

You MUST output only React code using Tailwind CSS and shadcn/ui components.
```

---

## USAGE

### Step 1: Generate the constraint block

Run the prompt template above, providing `LAYER-00-STRATEGIC-DECISIONS.md` as context.

### Step 2: Prepend to frontend-design prompts

When invoking Anthropic's `frontend-design` Skill or plugin, prepend the generated constraint block to your prompt:

```
[GENERATED CONSTRAINT BLOCK]

Now design a responsive dashboard layout with:
- Sidebar navigation
- Header with user menu
- Main content area with cards
```

### Step 3: Regenerate when stack changes

Whenever `LAYER-00-STRATEGIC-DECISIONS.md` is updated, regenerate the constraint block by running this prompt again. The new block will automatically reflect the updated stack.

---

## WHY THIS APPROACH?

1. **Single source of truth** — Stack decisions live in one document
2. **Intelligent extraction** — LLM understands context, not brittle string matching
3. **Automatic evolution** — Regenerate the constraint block whenever the source doc changes
4. **No modification to frontend-design** — Works with the upstream Skill as-is
5. **Portable** — The constraint block can be used anywhere: Claude, Claude Code, VS Code, etc.

---

## VALIDATION

After generating the constraint block, verify:

- [ ] All items from "What We Chose" appear in ALLOWED
- [ ] All items from "What We Rejected" appear in FORBIDDEN
- [ ] Version numbers are included where specified
- [ ] The SOURCE path is correct
- [ ] The final instruction references the correct framework/styling approach
