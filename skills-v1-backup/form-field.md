---
skill: ds-form-field
description: Add a form field with react-hook-form, Zod validation, and accessible error handling
triggers:
  - "add form field"
  - "create form field"
  - "ds form field"
  - "add input to form"
  - "form validation"
arguments:
  - name: field_name
    description: Name of the field (e.g., email, phone, username)
    required: true
  - name: field_type
    description: Input type (text, email, password, tel, select, checkbox, textarea)
    required: false
invoke_agent: form-agent
---

# Add Form Field

Dispatches to form-agent for creating form fields with validation.

The agent will:
1. Load PROMPTS/02-FORM-FIELD.md and APPLICATION-DOMAIN/12b-IMPLEMENTATION.md
2. Generate Zod schema for the field type
3. Create FormField component with proper structure
4. Add accessible labels and error handling
5. Include aria-describedby for error announcements
6. Validate against design system form patterns
