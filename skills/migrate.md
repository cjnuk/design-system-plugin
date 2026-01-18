---
skill: ds-migrate
description: Migrate components from Material UI, Chakra UI, or Bootstrap to shadcn/ui
triggers:
  - "migrate component"
  - "convert to shadcn"
  - "ds migrate"
  - "migrate from mui"
  - "migrate from chakra"
  - "migrate from bootstrap"
arguments:
  - name: source_framework
    description: Source framework (mui, chakra, bootstrap)
    required: false
  - name: component
    description: Component to migrate
    required: false
---

# Migrate to shadcn/ui

Convert components from Material UI, Chakra UI, or Bootstrap to shadcn/ui with semantic tokens.

## Migration Strategy

1. **Identify the component** - Find the shadcn/ui equivalent
2. **Map props** - Convert framework-specific props to shadcn/ui patterns
3. **Replace tokens** - Convert colors/spacing to semantic tokens
4. **Add variants** - Use cva for any custom variants
5. **Verify accessibility** - Ensure WCAG 2.1 AA compliance

## Component Mapping

### Buttons

| Source | shadcn/ui |
|--------|-----------|
| MUI `<Button variant="contained">` | `<Button variant="default">` |
| MUI `<Button variant="outlined">` | `<Button variant="outline">` |
| MUI `<Button variant="text">` | `<Button variant="ghost">` |
| Chakra `<Button colorScheme="blue">` | `<Button variant="default">` |
| Chakra `<Button variant="ghost">` | `<Button variant="ghost">` |
| Bootstrap `<button class="btn btn-primary">` | `<Button variant="default">` |
| Bootstrap `<button class="btn btn-outline-primary">` | `<Button variant="outline">` |

#### MUI Button Migration

```typescript
// BEFORE (MUI)
import Button from '@mui/material/Button'

<Button variant="contained" color="primary" size="large">
  Submit
</Button>

// AFTER (shadcn/ui)
import { Button } from "@/components/ui/button"

<Button variant="default" size="lg">
  Submit
</Button>
```

#### Chakra Button Migration

```typescript
// BEFORE (Chakra)
import { Button } from '@chakra-ui/react'

<Button colorScheme="blue" size="lg" isLoading>
  Submit
</Button>

// AFTER (shadcn/ui)
import { Button } from "@/components/ui/button"
import { Loader2 } from "lucide-react"

<Button size="lg" disabled>
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Submit
</Button>
```

### Forms

#### MUI TextField Migration

```typescript
// BEFORE (MUI)
import TextField from '@mui/material/TextField'

<TextField
  label="Email"
  type="email"
  error={!!errors.email}
  helperText={errors.email?.message}
/>

// AFTER (shadcn/ui)
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

<div className="space-y-2">
  <Label htmlFor="email">Email</Label>
  <Input
    id="email"
    type="email"
    {...register("email")}
    aria-invalid={!!errors.email}
  />
  {errors.email && (
    <p className="text-sm text-destructive">{errors.email.message}</p>
  )}
</div>

// Or with Form component
<FormField
  control={form.control}
  name="email"
  render={({ field }) => (
    <FormItem>
      <FormLabel>Email</FormLabel>
      <FormControl>
        <Input type="email" {...field} />
      </FormControl>
      <FormMessage />
    </FormItem>
  )}
/>
```

### Dialogs/Modals

#### MUI Dialog Migration

```typescript
// BEFORE (MUI)
import { Dialog, DialogTitle, DialogContent, DialogActions } from '@mui/material'

<Dialog open={open} onClose={handleClose}>
  <DialogTitle>Confirm</DialogTitle>
  <DialogContent>Are you sure?</DialogContent>
  <DialogActions>
    <Button onClick={handleClose}>Cancel</Button>
    <Button onClick={handleConfirm}>Confirm</Button>
  </DialogActions>
</Dialog>

// AFTER (shadcn/ui)
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogFooter,
  DialogHeader,
  DialogTitle,
} from "@/components/ui/dialog"

<Dialog open={open} onOpenChange={setOpen}>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Confirm</DialogTitle>
      <DialogDescription>Are you sure?</DialogDescription>
    </DialogHeader>
    <DialogFooter>
      <Button variant="outline" onClick={() => setOpen(false)}>
        Cancel
      </Button>
      <Button onClick={handleConfirm}>Confirm</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### Cards

#### Bootstrap Card Migration

```html
<!-- BEFORE (Bootstrap) -->
<div class="card">
  <div class="card-header">Featured</div>
  <div class="card-body">
    <h5 class="card-title">Card title</h5>
    <p class="card-text">Some quick example text.</p>
    <a href="#" class="btn btn-primary">Go somewhere</a>
  </div>
</div>
```

```typescript
// AFTER (shadcn/ui)
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card"
import { Button } from "@/components/ui/button"

<Card>
  <CardHeader>
    <CardTitle>Card title</CardTitle>
    <CardDescription>Featured</CardDescription>
  </CardHeader>
  <CardContent>
    <p>Some quick example text.</p>
  </CardContent>
  <CardFooter>
    <Button>Go somewhere</Button>
  </CardFooter>
</Card>
```

### Alerts

#### Chakra Alert Migration

```typescript
// BEFORE (Chakra)
import { Alert, AlertIcon, AlertTitle, AlertDescription } from '@chakra-ui/react'

<Alert status="error">
  <AlertIcon />
  <AlertTitle>Error!</AlertTitle>
  <AlertDescription>Something went wrong.</AlertDescription>
</Alert>

// AFTER (shadcn/ui)
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert"
import { AlertCircle } from "lucide-react"

<Alert variant="destructive">
  <AlertCircle className="h-4 w-4" />
  <AlertTitle>Error!</AlertTitle>
  <AlertDescription>Something went wrong.</AlertDescription>
</Alert>
```

## Color Token Migration

### MUI → Semantic Tokens

| MUI | shadcn/ui |
|-----|-----------|
| `primary.main` | `primary` |
| `secondary.main` | `secondary` |
| `error.main` | `destructive` |
| `grey[100]` | `muted` |
| `grey[500]` | `muted-foreground` |
| `text.primary` | `foreground` |
| `text.secondary` | `muted-foreground` |
| `background.paper` | `card` |
| `background.default` | `background` |

### Chakra → Semantic Tokens

| Chakra | shadcn/ui |
|--------|-----------|
| `blue.500` | `primary` |
| `gray.100` | `muted` |
| `gray.600` | `muted-foreground` |
| `red.500` | `destructive` |
| `white` | `background` |
| `gray.800` | `foreground` |

### Bootstrap → Semantic Tokens

| Bootstrap | shadcn/ui |
|-----------|-----------|
| `$primary` | `primary` |
| `$secondary` | `secondary` |
| `$danger` | `destructive` |
| `$light` | `muted` |
| `$dark` | `foreground` |
| `$body-bg` | `background` |

## Size/Spacing Migration

| Source | shadcn/ui |
|--------|-----------|
| MUI `size="small"` | `size="sm"` |
| MUI `size="medium"` | `size="default"` |
| MUI `size="large"` | `size="lg"` |
| Chakra `size="sm"` | `size="sm"` |
| Chakra `size="md"` | `size="default"` |
| Chakra `size="lg"` | `size="lg"` |
| Bootstrap `btn-sm` | `size="sm"` |
| Bootstrap `btn-lg` | `size="lg"` |

## Migration Checklist

- [ ] Replace imports with shadcn/ui components
- [ ] Map variant/color props to shadcn variants
- [ ] Convert size props (small→sm, medium→default, large→lg)
- [ ] Replace inline styles with Tailwind classes
- [ ] Replace theme colors with semantic tokens
- [ ] Add proper TypeScript types
- [ ] Test keyboard navigation
- [ ] Test with screen reader
- [ ] Run jest-axe tests

## Common Pitfalls

1. **Don't migrate complex components 1:1** - shadcn/ui may compose differently
2. **Check controlled vs uncontrolled** - State management may differ
3. **Verify event handlers** - Event signatures may change
4. **Test responsiveness** - Breakpoint systems differ
5. **Audit custom styles** - Framework-specific overrides won't work

## Dependencies to Update

```bash
# Remove old framework
npm uninstall @mui/material @emotion/react @emotion/styled
# or
npm uninstall @chakra-ui/react @emotion/react @emotion/styled
# or
npm uninstall bootstrap

# Add shadcn/ui dependencies (done by init)
npm install class-variance-authority clsx tailwind-merge
npm install @radix-ui/react-dialog @radix-ui/react-slot # etc.
```
