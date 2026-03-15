---
title: "Prompt: Add Form Field"
task_type: "form-implementation"
retrieval_path: ["LLM-AGENT-GUIDE", "LAYER-02-COMPONENTS", "APPLICATION-DOMAIN/12b-IMPLEMENTATION"]
---

# PROMPT: ADD FORM FIELD

Use this prompt when asking an LLM to add a new form field with validation.

---

## PROMPT TEMPLATE

```
Add a [FIELD_NAME] field to the [FORM_NAME] form.

Field requirements:
- Type: [text/email/password/select/checkbox/etc.]
- Validation: [required, min/max length, pattern, etc.]
- Label: "[LABEL_TEXT]"
- Placeholder: "[PLACEHOLDER_TEXT]"
- Error messages: [List specific error messages]

Technical constraints:
- Use react-hook-form with zodResolver
- Use shadcn/ui Form components
- Follow existing form patterns in the codebase
- Ensure accessible error announcements

Reference files:
- system/LAYER-02-COMPONENTS.md for form anatomy
- system/APPLICATION-DOMAIN/12b-IMPLEMENTATION.md for form patterns
```

---

## EXAMPLE USAGE

```
Add a phone field to the registration form.

Field requirements:
- Type: tel
- Validation: required, must match US phone format
- Label: "Phone Number"
- Placeholder: "(555) 123-4567"
- Error messages:
  - Required: "Phone number is required"
  - Invalid: "Please enter a valid US phone number"

Technical constraints:
- Use react-hook-form with zodResolver
- Use shadcn/ui Form components
- Follow existing form patterns in the codebase
- Ensure accessible error announcements
```

---

## EXPECTED OUTPUT

### 1. Schema Update

```typescript
const formSchema = z.object({
  // existing fields...
  phone: z
    .string()
    .min(1, "Phone number is required")
    .regex(/^\(\d{3}\) \d{3}-\d{4}$/, "Please enter a valid US phone number"),
})
```

### 2. Form Field

```typescript
<FormField
  control={form.control}
  name="phone"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Phone Number</FormLabel>
      <FormControl>
        <Input type="tel" placeholder="(555) 123-4567" {...field} />
      </FormControl>
      <FormMessage />
    </FormItem>
  )}
/>
```

---

## VALIDATION CHECKLIST

- [ ] Zod schema includes proper validation
- [ ] Error messages are user-friendly
- [ ] Uses FormField/FormItem/FormLabel/FormControl/FormMessage
- [ ] Input has correct type attribute
- [ ] Label is associated with input (via FormLabel)
- [ ] Errors are announced to screen readers
