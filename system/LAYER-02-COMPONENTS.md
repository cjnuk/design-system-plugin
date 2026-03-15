# LAYER 02: COMPONENTS

**Purpose:** Complete specification of all components (50+ from shadcn/ui + 10+ custom AI-specific components). This defines what you can build and how to use it.

---

## COMPONENT INVENTORY

### Core Components (shadcn/ui Foundation)

#### Buttons & Actions
- **Button** — Primary action element
- **IconButton** — Icon-only button for compact interfaces
- **SplitButton** — Button with dropdown menu
- **FloatingActionButton** — Fixed action button (FAB)

#### Form Controls
- **Input** — Text input field
- **Textarea** — Multi-line text input
- **Select** — Dropdown selection
- **Checkbox** — Boolean selection
- **Radio** — Single choice from group
- **Toggle** — On/off switch
- **Switch** — True/false toggle
- **Slider** — Continuous value selection
- **DatePicker** — Date selection
- **TimePicker** — Time selection
- **ComboBox** — Filterable dropdown

#### Form Layout
- **Form** — Form wrapper with validation
- **FormField** — Individual field container
- **Label** — Input labels
- **Description** — Helpful text below fields
- **ErrorMessage** — Validation error display
- **FormGroup** — Related fields grouping

#### Form Input Rules

Essential rules for usable, accessible forms:

1. **Never disable paste** on any input field
2. **16px minimum font size** on mobile (prevents iOS zoom on focus)
3. **Label every control** - no placeholder-only inputs
4. **Autocomplete attributes** - use `autocomplete="email"`, `autocomplete="current-password"`, etc.
5. **Input modes** - use `inputMode="numeric"` for number-only fields on mobile

```tsx
// CORRECT: Mobile-friendly input with all best practices
<div>
  <label htmlFor="email">Email address</label>
  <input
    id="email"
    type="email"
    autoComplete="email"
    inputMode="email"
    className="text-base md:text-sm" // 16px on mobile, 14px on desktop
    placeholder="you@example.com"
  />
</div>

// INCORRECT: Placeholder-only, small text, no autocomplete
<input
  type="email"
  placeholder="Email"
  className="text-xs"
/>
```

**See also:** [LAYER-08-ACCESSIBILITY.md](./LAYER-08-ACCESSIBILITY.md) for touch target requirements and comprehensive form accessibility guidelines.

#### Dialogs & Overlays
- **Dialog** — Modal dialog
- **AlertDialog** — Alert/confirmation dialog
- **Drawer** — Side panel (left/right/top/bottom)
- **Sheet** — Bottom sheet (mobile-friendly)
- **Popover** — Floating content box
- **Tooltip** — Hover information
- **ContextMenu** — Right-click menu
- **DropdownMenu** — Button dropdown menu

#### Data Display
- **Table** — Basic data table
- **DataTable** — Advanced table (sorting, filtering, pagination)
- **Card** — Content container
- **Badge** — Label/tag
- **Avatar** — User/entity image
- **AvatarGroup** — Multiple avatars
- **Progress** — Progress bar/indicator
- **Skeleton** — Content loading placeholder
- **Stat** — Single metric display

#### Navigation
- **Tabs** — Tabbed content sections
- **Breadcrumb** — Navigation trail
- **Pagination** — Page navigation
- **Stepper** — Multi-step process
- **Menu** — Navigation menu
- **NavigationMenu** — Complex navigation structure

#### Feedback
- **Toast** — Transient notification
- **Alert** — Persistent notification
- **AlertDialog** — Modal alert (see Dialogs)
- **Progress** — Loading progress (see Data Display)
- **Spinner** — Loading indicator

#### Layout
- **Separator** — Visual divider
- **Accordion** — Collapsible sections
- **Collapsible** — Expand/collapse content
- **Container** — Content width limiter
- **Grid** — CSS Grid wrapper
- **Flex** — Flexbox wrapper
- **Stack** — Vertical/horizontal stack
- **Spacer** — Spacing utility component

---

## CUSTOM AI-SPECIFIC COMPONENTS

### ChatWindow
**Purpose:** Main chat interface for AI conversations

```typescript
interface ChatWindowProps {
  messages: Message[];
  onSendMessage: (text: string) => void;
  isLoading?: boolean;
  error?: string;
  placeholder?: string;
}
```

**Features:**
- Message list with auto-scroll
- Typing indicator
- Message input with send button
- Error state display
- Loading state with skeleton messages

**Accessibility:**
- `role="region" aria-label="Chat messages"`
- Keyboard navigation (Tab, Enter to send)
- Screen reader friendly

---

### MessageBubble
**Purpose:** Individual message in chat

```typescript
interface MessageBubbleProps {
  message: Message;
  isUser: boolean;
  showTimestamp?: boolean;
}

interface Message {
  id: string;
  content: string;
  timestamp: Date;
  role: 'user' | 'assistant';
}
```

**States:**
- Default (displayed)
- Loading (streaming)
- Error (failed)
- Edited (shows "edited" indicator)

---

### SourcesPanel
**Purpose:** Display sources/citations for AI responses

```typescript
interface SourcesPanelProps {
  sources: Source[];
  isLoading?: boolean;
  onSourceClick?: (source: Source) => void;
}

interface Source {
  id: string;
  title: string;
  url: string;
  excerpt: string;
  relevance?: 'high' | 'medium' | 'low';
}
```

**Features:**
- Expandable source cards
- Search/filter sources
- Link handling
- Relevance indicators

---

### CodeBlock
**Purpose:** Syntax-highlighted code display

```typescript
interface CodeBlockProps {
  code: string;
  language: string;
  showLineNumbers?: boolean;
  copyable?: boolean;
  onCopy?: () => void;
}
```

**Features:**
- Syntax highlighting (Prism.js)
- Copy to clipboard button
- Line numbers
- Dark/light theme support
- Language badge

---

### LoadingState
**Purpose:** Loading state with skeleton screens

```typescript
interface LoadingStateProps {
  variant: 'skeleton' | 'spinner' | 'dots' | 'bar';
  count?: number; // for skeleton lines
}
```

**Variants:**
- Skeleton (placeholder blocks)
- Spinner (rotating indicator)
- Dots (bouncing dots)
- Bar (horizontal progress)

---

### ErrorBoundary
**Purpose:** Error catching and display

```typescript
interface ErrorBoundaryProps {
  children: React.ReactNode;
  onError?: (error: Error) => void;
  fallback?: React.ReactNode;
}
```

**Features:**
- Catches child errors
- Shows error message
- Retry button
- Logs to error tracking

---

### SidePanel
**Purpose:** Configurable side panel (settings, filters, etc.)

```typescript
interface SidePanelProps {
  isOpen: boolean;
  onClose: () => void;
  title?: string;
  width?: 'sm' | 'md' | 'lg'; // 300px, 400px, 500px
  position?: 'left' | 'right';
  children: React.ReactNode;
}
```

**States:**
- Open/closed
- Mobile (full width)
- Desktop (fixed width)

---

### EmptyState
**Purpose:** Empty result display

```typescript
interface EmptyStateProps {
  icon?: React.ReactNode;
  title: string;
  description?: string;
  action?: React.ReactNode;
}
```

**Examples:**
- No conversations yet
- No results found
- No permissions

---

---

## COMPONENT USAGE PATTERNS

### Basic Button Usage

```tsx
// Primary button
<Button variant="primary" size="md">
  Click me
</Button>

// Secondary button
<Button variant="secondary">
  Cancel
</Button>

// Outline button
<Button variant="outline">
  Learn more
</Button>

// Disabled state
<Button disabled>
  Disabled
</Button>

// Loading state
<Button isLoading>
  {isLoading ? 'Loading...' : 'Send'}
</Button>
```

### Form Usage

```tsx
<Form onSubmit={handleSubmit}>
  <FormField
    label="Email"
    error={errors.email}
  >
    <Input
      type="email"
      placeholder="you@example.com"
      {...register('email')}
    />
  </FormField>

  <FormField
    label="Message"
  >
    <Textarea
      placeholder="Your message"
      rows={4}
      {...register('message')}
    />
  </FormField>

  <Button type="submit">
    Send
  </Button>
</Form>
```

### Dialog Usage

```tsx
<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <DialogTrigger asChild>
    <Button>Open Dialog</Button>
  </DialogTrigger>
  
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Confirm Action</DialogTitle>
    </DialogHeader>
    
    <p>Are you sure?</p>
    
    <DialogFooter>
      <Button variant="outline" onClick={() => setIsOpen(false)}>
        Cancel
      </Button>
      <Button onClick={handleConfirm}>
        Confirm
      </Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### Table Usage

```tsx
<Table>
  <TableHeader>
    <TableRow>
      <TableHead>Name</TableHead>
      <TableHead>Email</TableHead>
      <TableHead>Role</TableHead>
    </TableRow>
  </TableHeader>
  
  <TableBody>
    {users.map(user => (
      <TableRow key={user.id}>
        <TableCell>{user.name}</TableCell>
        <TableCell>{user.email}</TableCell>
        <TableCell>{user.role}</TableCell>
      </TableRow>
    ))}
  </TableBody>
</Table>
```

---

## COMPONENT PROPS REFERENCE (GENERATED)

Component props must be sourced from generated documentation to prevent drift.

**Recommended outputs:**
- `docs/components/index.html` (Storybook Docs)
- `docs/components.json` (machine-readable metadata)

**LLM usage rule:** Prefer generated docs for prop details. Use this document for high-level intent and usage patterns.

**Example generation:**
```bash
# Storybook (example)
storybook build --docs

# TypeDoc (example)
typedoc --out docs/components --json docs/components.json src/components
```

---

## ACCESSIBILITY REQUIREMENTS

### All Components Must:
- ✅ Have proper ARIA labels
- ✅ Support keyboard navigation
- ✅ Have visible focus indicators
- ✅ Use semantic HTML
- ✅ Have sufficient color contrast (4.5:1)
- ✅ Be testable with jest-axe

### Button Accessibility
```tsx
<button
  aria-label="Close dialog"
  onClick={handleClose}
>
  ×
</button>
```

### Form Accessibility
```tsx
<label htmlFor="email">
  Email
</label>
<input
  id="email"
  type="email"
  aria-invalid={!!error}
  aria-describedby={error ? 'email-error' : undefined}
/>
{error && (
  <span id="email-error" role="alert">
    {error}
  </span>
)}
```

### Dialog Accessibility
```tsx
<div
  role="dialog"
  aria-labelledby="dialog-title"
  aria-modal="true"
>
  <h2 id="dialog-title">Confirm Action</h2>
  {/* content */}
</div>
```

---

## COMPONENT VARIANTS

### Button Variants

**Primary**
- Background: blue-500
- Text: white
- Hover: blue-600
- Used for: Main actions

**Secondary**
- Background: slate-200
- Text: slate-900
- Hover: slate-300
- Used for: Secondary actions

**Outline**
- Background: transparent
- Border: blue-500
- Text: blue-500
- Hover: blue-50
- Used for: Optional actions

**Ghost**
- Background: transparent
- Text: slate-600
- Hover: slate-100
- Used for: Tertiary actions, icon buttons

**Danger**
- Background: red-500
- Text: white
- Hover: red-600
- Used for: Destructive actions

---

## WHEN TO USE EACH COMPONENT

| Need | Component | Why |
|------|-----------|-----|
| **Action in UI** | Button | Semantic, accessible, flexible |
| **Text input** | Input | Standard form field |
| **Multiple lines** | Textarea | Better for longer text |
| **Choice from list** | Select | Cleaner than radios when many options |
| **One of few** | Radio | When options are few (2-4) |
| **Yes/no** | Checkbox | For boolean choices |
| **Confirmation** | AlertDialog | Modal feels appropriate for critical actions |
| **Side info** | Tooltip | Non-essential info on hover |
| **Modal info** | Dialog | Important content that needs attention |
| **Data list** | Table | Structured data display |
| **Progress** | Progress | Show operation completion % |
| **Loading** | Skeleton | Placeholder while loading |
| **Error message** | Alert | Inline error display |
| **Temporary message** | Toast | Non-blocking notification |

---

## CUSTOM COMPONENTS LOCATION

All custom components should be in:
```
src/components/
├── ui/                    # shadcn/ui components (auto-generated)
├── custom/                # Custom components
│   ├── ChatWindow.tsx
│   ├── MessageBubble.tsx
│   ├── SourcesPanel.tsx
│   ├── CodeBlock.tsx
│   ├── LoadingState.tsx
│   ├── ErrorBoundary.tsx
│   ├── SidePanel.tsx
│   └── EmptyState.tsx
```

---

## TESTING COMPONENTS

Every component should have:

```typescript
// Button.test.tsx
describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByText('Click me')).toBeInTheDocument();
  });

  it('handles click events', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByText('Click'));
    expect(handleClick).toHaveBeenCalled();
  });

  it('has proper accessibility', () => {
    const { container } = render(<Button>Click</Button>);
    expect(container).toHaveNoA11yViolations();
  });
});
```

---

## NEXT STEPS

1. **Set up shadcn/ui:** `npx shadcn@latest init`
2. **Add components:** `npx shadcn@latest add button input select dialog`
3. **Create custom components:** Follow the patterns in this layer
4. **Read [LAYER-04-CSS-ARCHITECTURE.md](./LAYER-04-CSS-ARCHITECTURE.md)** for styling details
