---
title: "Prompt: Migrate to Design System"
task_type: "migration"
retrieval_path: ["LLM-AGENT-GUIDE", "APPENDICES/APPENDIX-E-MIGRATION-PATHS", "LAYER-01-DESIGN-TOKENS", "LAYER-02-COMPONENTS"]
---

# PROMPT: MIGRATE TO DESIGN SYSTEM

Use this prompt when asking an LLM to migrate existing components to the design system.

---

## PROMPT TEMPLATE

```
Migrate [COMPONENT/FILE] to use our design system.

Current implementation:
- Framework: [MUI/Chakra/Bootstrap/custom/etc.]
- [Describe current patterns]

Migration requirements:
- Replace with shadcn/ui equivalents
- Convert to semantic token usage
- Maintain existing functionality
- Preserve accessibility features

Reference files:
- system/APPENDICES/APPENDIX-E-MIGRATION-PATHS.md for migration guides
- system/LAYER-01-DESIGN-TOKENS.md for token mapping
- system/LAYER-02-COMPONENTS.md for component equivalents
```

---

## COMMON MIGRATION MAPPINGS

### From MUI

| MUI | shadcn/ui |
|-----|-----------|
| `<Button variant="contained">` | `<Button>` |
| `<Button variant="outlined">` | `<Button variant="outline">` |
| `<TextField>` | `<Input>` + `<Label>` |
| `<Dialog>` | `<Dialog>` |
| `<Snackbar>` | `<Toast>` via useToast |
| `<Select>` | `<Select>` |
| `<Checkbox>` | `<Checkbox>` |
| `<Switch>` | `<Switch>` |

### From Chakra UI

| Chakra | shadcn/ui |
|--------|-----------|
| `<Button colorScheme="blue">` | `<Button>` |
| `<Input>` | `<Input>` |
| `<Modal>` | `<Dialog>` |
| `<useToast()>` | `useToast()` |
| `<Menu>` | `<DropdownMenu>` |
| `<Tabs>` | `<Tabs>` |

### From Bootstrap/Tailwind UI

| Bootstrap/TW UI | shadcn/ui |
|-----------------|-----------|
| `btn btn-primary` | `<Button>` |
| `form-control` | `<Input>` |
| `modal` | `<Dialog>` |
| `alert` | `<Alert>` |
| `dropdown` | `<DropdownMenu>` |

---

## TOKEN MIGRATION

### Colors

```typescript
// MUI
sx={{ color: 'primary.main', bgcolor: 'grey.100' }}

// Chakra
color="blue.500" bg="gray.100"

// Bootstrap
className="text-primary bg-light"

// ✅ shadcn/ui (semantic tokens)
className="text-primary bg-muted"
```

### Spacing

```typescript
// MUI
sx={{ p: 2, m: 1 }}  // 16px, 8px

// Chakra
p={4} m={2}  // 16px, 8px

// ✅ Tailwind
className="p-4 m-2"  // 16px, 8px
```

---

## MIGRATION CHECKLIST

- [ ] Identify all components to migrate
- [ ] Map current components to shadcn/ui equivalents
- [ ] Install required shadcn/ui components
- [ ] Convert color/spacing to semantic tokens
- [ ] Update import statements
- [ ] Test functionality parity
- [ ] Verify accessibility maintained
- [ ] Remove old dependencies

---

## EXAMPLE MIGRATION

### Before (MUI)

```typescript
import { Button, TextField, Dialog, DialogTitle, DialogContent, DialogActions } from '@mui/material'

function LoginDialog({ open, onClose }) {
  return (
    <Dialog open={open} onClose={onClose}>
      <DialogTitle>Login</DialogTitle>
      <DialogContent>
        <TextField label="Email" fullWidth margin="normal" />
        <TextField label="Password" type="password" fullWidth margin="normal" />
      </DialogContent>
      <DialogActions>
        <Button onClick={onClose}>Cancel</Button>
        <Button variant="contained" onClick={handleLogin}>Login</Button>
      </DialogActions>
    </Dialog>
  )
}
```

### After (shadcn/ui)

```typescript
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogFooter } from '@/components/ui/dialog'

function LoginDialog({ open, onOpenChange }) {
  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Login</DialogTitle>
        </DialogHeader>
        <div className="space-y-4 py-4">
          <div className="space-y-2">
            <Label htmlFor="email">Email</Label>
            <Input id="email" type="email" />
          </div>
          <div className="space-y-2">
            <Label htmlFor="password">Password</Label>
            <Input id="password" type="password" />
          </div>
        </div>
        <DialogFooter>
          <Button variant="outline" onClick={() => onOpenChange(false)}>Cancel</Button>
          <Button onClick={handleLogin}>Login</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}
```
