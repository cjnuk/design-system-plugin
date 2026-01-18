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
---

# Add Form Field

Add a form field with proper validation using react-hook-form, Zod schema, and shadcn/ui Form components. Ensures accessible error announcements.

## Form Field Template

### 1. Update Zod Schema

```typescript
import { z } from "zod"

const formSchema = z.object({
  // Existing fields...

  // Add your new field:
  fieldName: z
    .string()
    .min(1, "Field is required")
    .max(100, "Maximum 100 characters"),
})

type FormValues = z.infer<typeof formSchema>
```

### 2. Add FormField Component

```typescript
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"
import { Input } from "@/components/ui/input"

<FormField
  control={form.control}
  name="fieldName"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Label Text</FormLabel>
      <FormControl>
        <Input
          type="text"
          placeholder="Enter value..."
          {...field}
        />
      </FormControl>
      <FormDescription>
        Optional helper text
      </FormDescription>
      <FormMessage />
    </FormItem>
  )}
/>
```

## Common Field Patterns

### Email Field

```typescript
// Schema
email: z
  .string()
  .min(1, "Email is required")
  .email("Please enter a valid email address"),

// Component
<FormField
  control={form.control}
  name="email"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Email</FormLabel>
      <FormControl>
        <Input type="email" placeholder="you@example.com" {...field} />
      </FormControl>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Password Field

```typescript
// Schema
password: z
  .string()
  .min(8, "Password must be at least 8 characters")
  .regex(/[A-Z]/, "Password must contain at least one uppercase letter")
  .regex(/[0-9]/, "Password must contain at least one number"),

// Component
<FormField
  control={form.control}
  name="password"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Password</FormLabel>
      <FormControl>
        <Input type="password" {...field} />
      </FormControl>
      <FormDescription>
        Minimum 8 characters with one uppercase and one number
      </FormDescription>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Phone Field

```typescript
// Schema
phone: z
  .string()
  .min(1, "Phone number is required")
  .regex(/^\(\d{3}\) \d{3}-\d{4}$/, "Please enter a valid phone number"),

// Component
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

### Select Field

```typescript
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select"

// Schema
country: z.string().min(1, "Please select a country"),

// Component
<FormField
  control={form.control}
  name="country"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Country</FormLabel>
      <Select onValueChange={field.onChange} defaultValue={field.value}>
        <FormControl>
          <SelectTrigger>
            <SelectValue placeholder="Select a country" />
          </SelectTrigger>
        </FormControl>
        <SelectContent>
          <SelectItem value="us">United States</SelectItem>
          <SelectItem value="uk">United Kingdom</SelectItem>
          <SelectItem value="ca">Canada</SelectItem>
        </SelectContent>
      </Select>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Checkbox Field

```typescript
import { Checkbox } from "@/components/ui/checkbox"

// Schema
acceptTerms: z.boolean().refine(val => val === true, {
  message: "You must accept the terms and conditions",
}),

// Component
<FormField
  control={form.control}
  name="acceptTerms"
  render={({ field }) => (
    <FormItem className="flex flex-row items-start space-x-3 space-y-0">
      <FormControl>
        <Checkbox
          checked={field.value}
          onCheckedChange={field.onChange}
        />
      </FormControl>
      <div className="space-y-1 leading-none">
        <FormLabel>
          Accept terms and conditions
        </FormLabel>
        <FormDescription>
          By checking this box, you agree to our Terms of Service
        </FormDescription>
      </div>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Textarea Field

```typescript
import { Textarea } from "@/components/ui/textarea"

// Schema
message: z
  .string()
  .min(10, "Message must be at least 10 characters")
  .max(500, "Message must be less than 500 characters"),

// Component
<FormField
  control={form.control}
  name="message"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Message</FormLabel>
      <FormControl>
        <Textarea
          placeholder="Enter your message..."
          className="resize-none"
          {...field}
        />
      </FormControl>
      <FormDescription>
        {field.value?.length || 0}/500 characters
      </FormDescription>
      <FormMessage />
    </FormItem>
  )}
/>
```

## Complete Form Example

```typescript
"use client"

import { zodResolver } from "@hookform/resolvers/zod"
import { useForm } from "react-hook-form"
import { z } from "zod"
import { Button } from "@/components/ui/button"
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form"
import { Input } from "@/components/ui/input"

const formSchema = z.object({
  username: z.string().min(2, "Username must be at least 2 characters"),
  email: z.string().email("Please enter a valid email"),
})

type FormValues = z.infer<typeof formSchema>

export function ProfileForm() {
  const form = useForm<FormValues>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      username: "",
      email: "",
    },
  })

  function onSubmit(values: FormValues) {
    console.log(values)
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-6">
        <FormField
          control={form.control}
          name="username"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Username</FormLabel>
              <FormControl>
                <Input placeholder="johndoe" {...field} />
              </FormControl>
              <FormDescription>
                This is your public display name.
              </FormDescription>
              <FormMessage />
            </FormItem>
          )}
        />

        <FormField
          control={form.control}
          name="email"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Email</FormLabel>
              <FormControl>
                <Input type="email" placeholder="you@example.com" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />

        <Button type="submit">Submit</Button>
      </form>
    </Form>
  )
}
```

## Accessibility Requirements

- [ ] Label is associated with input (FormLabel handles this)
- [ ] Error messages use `role="alert"` (FormMessage handles this)
- [ ] Input has correct `type` attribute
- [ ] Placeholder is not a substitute for label
- [ ] Required fields are indicated
- [ ] Error messages are announced to screen readers

## Validation Checklist

- [ ] Zod schema includes proper validation rules
- [ ] Error messages are user-friendly and specific
- [ ] Uses FormField/FormItem/FormLabel/FormControl/FormMessage
- [ ] Input has correct `type` attribute
- [ ] Select has accessible trigger and value
- [ ] Checkbox has associated label
