# LAYER 07b: PROMPT PATTERNS (LLM UI)

**File:** LAYER-07b-PROMPT-PATTERNS.md  
**Purpose:** Standardize how prompts, suggestions, and regeneration are presented in AI experiences  
**Audience:** Product designers, LLM agents, frontend developers  
**Last Updated:** January 2026

---

## TABLE OF CONTENTS

1. [Core Principles](#core-principles)
2. [Prompt Surfaces](#prompt-surfaces)
3. [Regeneration and Variants](#regeneration-and-variants)
4. [Structured Prompt Object](#structured-prompt-object-for-llm-and-ui)
5. [States and Feedback](#states-and-feedback)
6. [LLM Usage Rule](#llm-usage-rule)

---

## Core Principles

1. Make prompt intent explicit.
2. Keep regeneration actions deterministic and auditable.
3. Show provenance when possible (sources, tools, inputs).

---

## Prompt Surfaces

### Prompt Input
- Single-line input for quick prompts.
- Multiline input for structured or long prompts.
- Always show placeholder hints that match the domain.

### Suggestion Rail
- Horizontal chips for quick-start prompts.
- Max 6 suggestions per view.
- Include a "More" action if you need a second row.

### Prompt Cards
Use cards when prompts need additional context (goal, constraints, tone):
- Title (goal)
- Subtitle (constraints)
- CTA ("Use prompt")

---

## Regeneration and Variants

- Provide: `Regenerate`, `Regenerate with changes`, and `Keep` actions.
- When regenerating, show the delta: "Changed tone to concise".
- Avoid silent regeneration.

---

## Structured Prompt Object (for LLM and UI)

```json
{
  "id": "prompt_001",
  "title": "Summarize this design spec",
  "prompt": "Summarize the system in 5 bullets for a new engineer.",
  "constraints": ["5 bullets", "include accessibility"],
  "tone": "concise",
  "actions": ["use", "edit", "regenerate"]
}
```

---

## States and Feedback

- Loading: show token streaming or progress states.
- Error: surface error reason and provide retry.
- Success: show follow-up suggestions.

---

## LLM Usage Rule

When generating UI, always include a clear prompt input, a suggestion surface, and regeneration controls that log changes.
