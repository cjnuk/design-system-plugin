---
title: "12b: Implementation Guide"
version: "2.1"
audience: ["developers", "llm-agents"]
dependencies: ["09b-COMPONENTS", "10b-SETUP", "11b-TESTING"]
last_updated: "2026-01-18"
---

# 12b: IMPLEMENTATION GUIDE

**Purpose:** Step-by-step implementation workflows for common application features.

---

## AUTHENTICATION FLOW

### 1. Setup Auth Context

```typescript
// src/contexts/auth-context.tsx
import { createContext, useContext, useState, useCallback, type ReactNode } from 'react'

interface User {
  id: string
  email: string
  name: string
}

interface AuthContextValue {
  user: User | null
  isLoading: boolean
  login: (email: string, password: string) => Promise<void>
  logout: () => Promise<void>
}

const AuthContext = createContext<AuthContextValue | null>(null)

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null)
  const [isLoading, setIsLoading] = useState(true)

  const login = useCallback(async (email: string, password: string) => {
    const res = await fetch('/api/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password }),
    })
    if (!res.ok) throw new Error('Invalid credentials')
    setUser(await res.json())
  }, [])

  const logout = useCallback(async () => {
    await fetch('/api/auth/logout', { method: 'POST' })
    setUser(null)
  }, [])

  return (
    <AuthContext.Provider value={{ user, isLoading, login, logout }}>
      {children}
    </AuthContext.Provider>
  )
}

export function useAuth() {
  const ctx = useContext(AuthContext)
  if (!ctx) throw new Error('useAuth must be used within AuthProvider')
  return ctx
}
```

### 2. Login Form Component

```typescript
// src/components/features/login-form.tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Form, FormField, FormItem, FormLabel, FormControl, FormMessage } from '@/components/ui/form'
import { useAuth } from '@/contexts/auth-context'

const loginSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be 8+ characters'),
})

type LoginFormData = z.infer<typeof loginSchema>

export function LoginForm() {
  const { login } = useAuth()
  const form = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
    defaultValues: { email: '', password: '' },
  })

  const onSubmit = async (data: LoginFormData) => {
    try {
      await login(data.email, data.password)
    } catch (error) {
      form.setError('root', { message: 'Invalid credentials' })
    }
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
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
        <FormField
          control={form.control}
          name="password"
          render={({ field }) => (
            <FormItem>
              <FormLabel>Password</FormLabel>
              <FormControl>
                <Input type="password" {...field} />
              </FormControl>
              <FormMessage />
            </FormItem>
          )}
        />
        {form.formState.errors.root && (
          <p className="text-sm text-destructive">{form.formState.errors.root.message}</p>
        )}
        <Button type="submit" className="w-full" disabled={form.formState.isSubmitting}>
          {form.formState.isSubmitting ? 'Logging in...' : 'Log in'}
        </Button>
      </form>
    </Form>
  )
}
```

---

## DATA TABLE WITH TANSTACK TABLE

```typescript
// src/components/features/users-table.tsx
import { useReactTable, getCoreRowModel, getPaginationRowModel, flexRender, type ColumnDef } from '@tanstack/react-table'
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table'
import { Button } from '@/components/ui/button'

interface User {
  id: string
  name: string
  email: string
  role: string
}

const columns: ColumnDef<User>[] = [
  { accessorKey: 'name', header: 'Name' },
  { accessorKey: 'email', header: 'Email' },
  { accessorKey: 'role', header: 'Role' },
]

export function UsersTable({ data }: { data: User[] }) {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
  })

  return (
    <div className="space-y-4">
      <Table>
        <TableHeader>
          {table.getHeaderGroups().map(headerGroup => (
            <TableRow key={headerGroup.id}>
              {headerGroup.headers.map(header => (
                <TableHead key={header.id}>
                  {flexRender(header.column.columnDef.header, header.getContext())}
                </TableHead>
              ))}
            </TableRow>
          ))}
        </TableHeader>
        <TableBody>
          {table.getRowModel().rows.map(row => (
            <TableRow key={row.id}>
              {row.getVisibleCells().map(cell => (
                <TableCell key={cell.id}>
                  {flexRender(cell.column.columnDef.cell, cell.getContext())}
                </TableCell>
              ))}
            </TableRow>
          ))}
        </TableBody>
      </Table>
      <div className="flex justify-end gap-2">
        <Button variant="outline" size="sm" onClick={() => table.previousPage()} disabled={!table.getCanPreviousPage()}>
          Previous
        </Button>
        <Button variant="outline" size="sm" onClick={() => table.nextPage()} disabled={!table.getCanNextPage()}>
          Next
        </Button>
      </div>
    </div>
  )
}
```

---

## MODAL DIALOG PATTERN

```typescript
// src/components/features/confirm-dialog.tsx
import { AlertDialog, AlertDialogAction, AlertDialogCancel, AlertDialogContent, AlertDialogDescription, AlertDialogFooter, AlertDialogHeader, AlertDialogTitle, AlertDialogTrigger } from '@/components/ui/alert-dialog'
import { Button } from '@/components/ui/button'

interface ConfirmDialogProps {
  trigger: React.ReactNode
  title: string
  description: string
  onConfirm: () => void
  destructive?: boolean
}

export function ConfirmDialog({ trigger, title, description, onConfirm, destructive }: ConfirmDialogProps) {
  return (
    <AlertDialog>
      <AlertDialogTrigger asChild>{trigger}</AlertDialogTrigger>
      <AlertDialogContent>
        <AlertDialogHeader>
          <AlertDialogTitle>{title}</AlertDialogTitle>
          <AlertDialogDescription>{description}</AlertDialogDescription>
        </AlertDialogHeader>
        <AlertDialogFooter>
          <AlertDialogCancel>Cancel</AlertDialogCancel>
          <AlertDialogAction onClick={onConfirm} className={destructive ? 'bg-destructive text-destructive-foreground' : ''}>
            Confirm
          </AlertDialogAction>
        </AlertDialogFooter>
      </AlertDialogContent>
    </AlertDialog>
  )
}
```

---

## TOAST NOTIFICATIONS

```typescript
// src/hooks/use-toast-actions.ts
import { toast } from '@/hooks/use-toast'

export function useToastActions() {
  return {
    success: (message: string) => toast({ description: message }),
    error: (message: string) => toast({ variant: 'destructive', description: message }),
    promise: async <T,>(promise: Promise<T>, { loading, success, error }: { loading: string; success: string; error: string }) => {
      const { dismiss } = toast({ description: loading })
      try {
        const result = await promise
        dismiss()
        toast({ description: success })
        return result
      } catch (e) {
        dismiss()
        toast({ variant: 'destructive', description: error })
        throw e
      }
    },
  }
}
```

---

## GLOBAL STATE (JOTAI)

```typescript
// src/atoms/theme.ts
import { atom, useAtom } from 'jotai'
import { atomWithStorage } from 'jotai/utils'

type Theme = 'light' | 'dark' | 'system'

export const themeAtom = atomWithStorage<Theme>('theme', 'system')

export function useTheme() {
  return useAtom(themeAtom)
}
```

```typescript
// src/atoms/ui.ts
import { atom, useAtomValue, useSetAtom } from 'jotai'

export const sidebarOpenAtom = atom(true)
export const useSidebarOpen = () => useAtomValue(sidebarOpenAtom)
export const useSetSidebarOpen = () => useSetAtom(sidebarOpenAtom)
```

---

## MULTI-STEP FORMS (WIZARD)

### State Management

```typescript
// src/atoms/wizard.ts
import { atom, useAtom, useAtomValue, useSetAtom } from 'jotai'

interface WizardState<T> {
  currentStep: number
  totalSteps: number
  data: Partial<T>
  completedSteps: Set<number>
}

function createWizardAtom<T>(totalSteps: number) {
  return atom<WizardState<T>>({
    currentStep: 0,
    totalSteps,
    data: {},
    completedSteps: new Set(),
  })
}

// Example: 3-step signup wizard
export const signupWizardAtom = createWizardAtom<{
  email: string
  password: string
  name: string
  company: string
  plan: 'free' | 'pro' | 'enterprise'
}>(3)
```

### Wizard Container Component

```typescript
// src/components/features/wizard.tsx
import { useAtom } from 'jotai'
import { Button } from '@/components/ui/button'
import { Progress } from '@/components/ui/progress'
import { cn } from '@/lib/utils'

interface WizardProps<T> {
  atom: ReturnType<typeof atom<WizardState<T>>>
  steps: Array<{
    title: string
    description?: string
    component: React.ComponentType<{
      data: Partial<T>
      onUpdate: (data: Partial<T>) => void
    }>
    validate?: (data: Partial<T>) => boolean
  }>
  onComplete: (data: T) => void
}

export function Wizard<T>({ atom: wizardAtom, steps, onComplete }: WizardProps<T>) {
  const [state, setState] = useAtom(wizardAtom)
  const { currentStep, data, completedSteps } = state

  const CurrentStepComponent = steps[currentStep].component
  const isLastStep = currentStep === steps.length - 1
  const canProceed = steps[currentStep].validate?.(data) ?? true

  const handleNext = () => {
    if (isLastStep) {
      onComplete(data as T)
    } else {
      setState((prev) => ({
        ...prev,
        currentStep: prev.currentStep + 1,
        completedSteps: new Set([...prev.completedSteps, prev.currentStep]),
      }))
    }
  }

  const handleBack = () => {
    setState((prev) => ({
      ...prev,
      currentStep: Math.max(0, prev.currentStep - 1),
    }))
  }

  const handleUpdate = (newData: Partial<T>) => {
    setState((prev) => ({
      ...prev,
      data: { ...prev.data, ...newData },
    }))
  }

  return (
    <div className="space-y-8">
      {/* Progress indicator */}
      <div className="space-y-2">
        <Progress value={((currentStep + 1) / steps.length) * 100} />
        <div className="flex justify-between">
          {steps.map((step, index) => (
            <div
              key={index}
              className={cn(
                'text-sm',
                index <= currentStep ? 'text-primary' : 'text-muted-foreground'
              )}
            >
              {step.title}
            </div>
          ))}
        </div>
      </div>

      {/* Step content */}
      <div className="min-h-[300px]">
        <h2 className="text-xl font-semibold mb-2">{steps[currentStep].title}</h2>
        {steps[currentStep].description && (
          <p className="text-muted-foreground mb-6">{steps[currentStep].description}</p>
        )}
        <CurrentStepComponent data={data} onUpdate={handleUpdate} />
      </div>

      {/* Navigation */}
      <div className="flex justify-between">
        <Button
          variant="outline"
          onClick={handleBack}
          disabled={currentStep === 0}
        >
          Back
        </Button>
        <Button onClick={handleNext} disabled={!canProceed}>
          {isLastStep ? 'Complete' : 'Next'}
        </Button>
      </div>
    </div>
  )
}
```

### Step Components Example

```typescript
// src/components/features/signup-wizard/step-account.tsx
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'

interface AccountData {
  email: string
  password: string
}

export function StepAccount({
  data,
  onUpdate,
}: {
  data: Partial<AccountData>
  onUpdate: (data: Partial<AccountData>) => void
}) {
  return (
    <div className="space-y-4">
      <div className="space-y-2">
        <Label htmlFor="email">Email</Label>
        <Input
          id="email"
          type="email"
          value={data.email ?? ''}
          onChange={(e) => onUpdate({ email: e.target.value })}
          placeholder="you@example.com"
        />
      </div>
      <div className="space-y-2">
        <Label htmlFor="password">Password</Label>
        <Input
          id="password"
          type="password"
          value={data.password ?? ''}
          onChange={(e) => onUpdate({ password: e.target.value })}
          placeholder="At least 8 characters"
        />
      </div>
    </div>
  )
}
```

### Validation Strategy

```typescript
// Validate per-step before allowing navigation
const steps = [
  {
    title: 'Account',
    component: StepAccount,
    validate: (data) => {
      const email = data.email ?? ''
      const password = data.password ?? ''
      return email.includes('@') && password.length >= 8
    },
  },
  {
    title: 'Profile',
    component: StepProfile,
    validate: (data) => (data.name ?? '').length > 0,
  },
  {
    title: 'Plan',
    component: StepPlan,
    validate: (data) => data.plan !== undefined,
  },
]
```

### Usage

```typescript
// src/app/signup/page.tsx
import { Wizard } from '@/components/features/wizard'
import { signupWizardAtom } from '@/atoms/wizard'
import { StepAccount } from '@/components/features/signup-wizard/step-account'
import { StepProfile } from '@/components/features/signup-wizard/step-profile'
import { StepPlan } from '@/components/features/signup-wizard/step-plan'

type SignupData = {
  email: string
  password: string
  name: string
  company: string
  plan: 'free' | 'pro' | 'enterprise'
}

const steps = [
  {
    title: 'Account',
    component: StepAccount,
    validate: (data: Partial<SignupData>) =>
      Boolean(data.email?.includes('@') && (data.password?.length ?? 0) >= 8)
  },
  {
    title: 'Profile',
    component: StepProfile,
    validate: (data: Partial<SignupData>) => (data.name?.length ?? 0) > 0
  },
  {
    title: 'Plan',
    component: StepPlan,
    validate: (data: Partial<SignupData>) => data.plan !== undefined
  },
]

export default function SignupPage() {
  const handleComplete = async (data: SignupData) => {
    await fetch('/api/signup', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data),
    })
  }

  return (
    <div className="max-w-lg mx-auto p-8">
      <Wizard atom={signupWizardAtom} steps={steps} onComplete={handleComplete} />
    </div>
  )
}
```

---

## FILE STRUCTURE CONVENTION

```
src/
├── app/                    # Next.js App Router pages
│   ├── (auth)/             # Auth layout group
│   │   ├── login/
│   │   └── register/
│   ├── (dashboard)/        # Dashboard layout group
│   │   ├── layout.tsx
│   │   └── users/
│   └── layout.tsx
├── components/
│   ├── ui/                 # shadcn/ui components
│   └── features/           # Feature-specific components
├── contexts/               # React contexts
├── hooks/                  # Custom hooks
├── atoms/                  # Jotai atoms
├── lib/                    # Utilities (utils.ts, api.ts)
└── types/                  # TypeScript types
```

---

## IMPLEMENTATION CHECKLIST

1. **Project setup** → [10b-SETUP](./10b-SETUP.md)
2. **Install components** → `npx shadcn@latest add [component]`
3. **Configure theme** → [LAYER-01-DESIGN-TOKENS](../LAYER-01-DESIGN-TOKENS.md)
4. **Build features** → Combine UI components
5. **Add tests** → [11b-TESTING](./11b-TESTING.md)
6. **Check accessibility** → [LAYER-08-ACCESSIBILITY](../LAYER-08-ACCESSIBILITY.md)

---

**Related:** [09b-COMPONENTS](./09b-COMPONENTS.md) | [10b-SETUP](./10b-SETUP.md) | [11b-TESTING](./11b-TESTING.md)
