---
skill: ds-compound-component
description: Generate compound component patterns (Parent.Child syntax) for complex, composable UI components
triggers:
  - "compound component"
  - "create compound"
  - "ds compound"
  - "modal pattern"
  - "composable component"
---

# Compound Component Pattern

Generate compound component patterns for complex, composable UI components using the Parent.Child syntax.

## When to Use

- Components with multiple related sub-parts (Modal, Card, Menu)
- Components where users need flexible composition
- Components that share internal state between parts
- When you want a cleaner API than many props

## Pattern Overview

```tsx
// Usage example
<Modal>
  <Modal.Header>Title</Modal.Header>
  <Modal.Body>Content</Modal.Body>
  <Modal.Footer>
    <Button>Close</Button>
  </Modal.Footer>
</Modal>
```

## Implementation

### 1. Create Context for Shared State

```typescript
// src/components/ui/modal/modal-context.tsx
import { createContext, useContext } from 'react'

interface ModalContextValue {
  isOpen: boolean
  onClose: () => void
}

const ModalContext = createContext<ModalContextValue | null>(null)

export function useModalContext() {
  const context = useContext(ModalContext)
  if (!context) {
    throw new Error('Modal components must be used within a Modal')
  }
  return context
}

export { ModalContext }
```

### 2. Create Root Component

```typescript
// src/components/ui/modal/modal.tsx
import { useState, type ReactNode } from 'react'
import { ModalContext } from './modal-context'
import { Dialog, DialogContent, DialogOverlay, DialogPortal } from '@radix-ui/react-dialog'

interface ModalProps {
  children: ReactNode
  open?: boolean
  onOpenChange?: (open: boolean) => void
}

export function ModalRoot({ children, open, onOpenChange }: ModalProps) {
  const [internalOpen, setInternalOpen] = useState(false)

  const isOpen = open ?? internalOpen
  const handleOpenChange = onOpenChange ?? setInternalOpen

  return (
    <ModalContext.Provider value={{ isOpen, onClose: () => handleOpenChange(false) }}>
      <Dialog open={isOpen} onOpenChange={handleOpenChange}>
        <DialogPortal>
          <DialogOverlay className="fixed inset-0 bg-black/50 data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0" />
          <DialogContent className="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-background rounded-lg shadow-xl w-full max-w-lg">
            {children}
          </DialogContent>
        </DialogPortal>
      </Dialog>
    </ModalContext.Provider>
  )
}

export type { ModalProps }
```

### 3. Create Sub-Components

```typescript
// src/components/ui/modal/modal-header.tsx
import type { ReactNode } from 'react'
import { DialogTitle } from '@radix-ui/react-dialog'
import { X } from 'lucide-react'
import { Button } from '@/components/ui/button'
import { useModalContext } from './modal-context'

interface ModalHeaderProps {
  children: ReactNode
  showClose?: boolean
}

export function ModalHeader({ children, showClose = true }: ModalHeaderProps) {
  const { onClose } = useModalContext()

  return (
    <div className="flex items-center justify-between p-4 border-b">
      <DialogTitle className="text-lg font-semibold">
        {children}
      </DialogTitle>
      {showClose && (
        <Button variant="ghost" size="sm" onClick={onClose} aria-label="Close">
          <X className="h-4 w-4" />
        </Button>
      )}
    </div>
  )
}
```

```typescript
// src/components/ui/modal/modal-body.tsx
import type { ReactNode } from 'react'

interface ModalBodyProps {
  children: ReactNode
  className?: string
}

export function ModalBody({ children, className }: ModalBodyProps) {
  return (
    <div className={`p-4 ${className ?? ''}`}>
      {children}
    </div>
  )
}
```

```typescript
// src/components/ui/modal/modal-footer.tsx
import type { ReactNode } from 'react'

interface ModalFooterProps {
  children: ReactNode
}

export function ModalFooter({ children }: ModalFooterProps) {
  return (
    <div className="flex justify-end gap-2 p-4 border-t">
      {children}
    </div>
  )
}
```

### 4. Assemble with Static Properties

```typescript
// src/components/ui/modal/index.tsx
import { ModalRoot } from './modal'
import { ModalHeader } from './modal-header'
import { ModalBody } from './modal-body'
import { ModalFooter } from './modal-footer'

// Attach sub-components as static properties
export const Modal = Object.assign(ModalRoot, {
  Header: ModalHeader,
  Body: ModalBody,
  Footer: ModalFooter,
})

// Export types
export type { ModalProps } from './modal'
```

## Complete Usage Example

```tsx
import { useState } from 'react'
import { Modal } from '@/components/ui/modal'
import { Button } from '@/components/ui/button'

export function DeleteConfirmation() {
  const [open, setOpen] = useState(false)

  const handleDelete = async () => {
    await deleteItem()
    setOpen(false)
  }

  return (
    <>
      <Button variant="destructive" onClick={() => setOpen(true)}>
        Delete
      </Button>

      <Modal open={open} onOpenChange={setOpen}>
        <Modal.Header>Confirm Deletion</Modal.Header>
        <Modal.Body>
          Are you sure you want to delete this item? This action cannot be undone.
        </Modal.Body>
        <Modal.Footer>
          <Button variant="outline" onClick={() => setOpen(false)}>
            Cancel
          </Button>
          <Button variant="destructive" onClick={handleDelete}>
            Delete
          </Button>
        </Modal.Footer>
      </Modal>
    </>
  )
}
```

## Benefits

| Benefit | Description |
|---------|-------------|
| Composition | Users arrange parts flexibly |
| Encapsulation | Internal state managed by parent |
| Type Safety | Each part has typed props |
| Discoverability | IDE autocomplete shows available parts |
| Testability | Each part can be tested independently |

## Common Compound Components

- **Modal**: Header, Body, Footer, Trigger
- **Card**: Header, Content, Footer, Image
- **Menu**: Trigger, Content, Item, Separator
- **Tabs**: List, Trigger, Content
- **Accordion**: Item, Trigger, Content

## Next Steps

- See [LAYER-02-COMPONENTS](../../system/LAYER-02-COMPONENTS.md) for component inventory
- Use `/ds-component` for simpler single-part components
