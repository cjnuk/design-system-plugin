# LAYER-07: INTERACTION PATTERNS

**File:** LAYER-07-INTERACTION-PATTERNS.md  
**Purpose:** Documented patterns for loading, error, and success states  
**Audience:** Product designers, frontend developers, QA  
**Last Updated:** January 18, 2026

---

## TABLE OF CONTENTS

1. [Loading States](#loading-states)
2. [Error States](#error-states)
3. [Success States](#success-states)
4. [Animation Timing](#animation-timing)
5. [Animation Performance](#animation-performance)
6. [Micro-interactions](#micro-interactions)
7. [Keyboard Interactions](#keyboard-interactions)
8. [Complete Examples](#complete-examples)

---

## LOADING STATES

### Pattern 1: Skeleton Loading (Preferred for Data)

Use skeletons to show content shape while loading.

```jsx
import { Skeleton } from '@/components/ui/skeleton';

export function UserCardSkeleton() {
  return (
    <div className="space-y-4">
      <Skeleton className="w-12 h-12 rounded-full" /> {/* Avatar */}
      <Skeleton className="w-32 h-6" /> {/* Name */}
      <Skeleton className="w-48 h-4" /> {/* Email */}
    </div>
  );
}

export function UserCard({ user, isLoading }) {
  if (isLoading) {
    return <UserCardSkeleton />;
  }

  return (
    <div className="space-y-4">
      <img src={user.avatar} className="w-12 h-12 rounded-full" />
      <h2 className="text-lg font-semibold">{user.name}</h2>
      <p className="text-gray-600">{user.email}</p>
    </div>
  );
}
```

### Pattern 2: Progress Indicator (For Long Operations)

```jsx
import { Progress } from '@/components/ui/progress';

export function FileUpload() {
  const [progress, setProgress] = useState(0);
  const [isUploading, setIsUploading] = useState(false);

  const handleUpload = async (file) => {
    setIsUploading(true);
    const xhr = new XMLHttpRequest();
    
    xhr.upload.addEventListener('progress', (e) => {
      const percentComplete = (e.loaded / e.total) * 100;
      setProgress(percentComplete);
    });

    xhr.addEventListener('load', () => {
      setIsUploading(false);
      setProgress(100);
    });

    const formData = new FormData();
    formData.append('file', file);
    xhr.open('POST', '/api/upload');
    xhr.send(formData);
  };

  if (!isUploading) {
    return <input type="file" onChange={e => handleUpload(e.target.files[0])} />;
  }

  return (
    <div className="space-y-2">
      <Progress value={progress} />
      <p className="text-sm text-gray-600">{Math.round(progress)}%</p>
    </div>
  );
}
```

### Pattern 3: Spinner (For Indeterminate Loading)

```jsx
import { Spinner } from '@/components/ui/spinner';

export function DataLoader() {
  const [isLoading, setIsLoading] = useState(true);

  return (
    <div>
      {isLoading ? (
        <div className="flex items-center justify-center p-8">
          <Spinner size="lg" />
          <p className="ml-4 text-gray-600">Loading data...</p>
        </div>
      ) : (
        <p>Data loaded!</p>
      )}
    </div>
  );
}
```

### Loading Indicator Timing

**Rule:** Delay loading indicators by 150-300ms to prevent flicker on fast operations.

```typescript
const LOADING_DELAY_MS = 200;

function useDelayedLoading(isLoading: boolean) {
  const [showLoading, setShowLoading] = useState(false);

  useEffect(() => {
    let timeout: NodeJS.Timeout;
    if (isLoading) {
      timeout = setTimeout(() => setShowLoading(true), LOADING_DELAY_MS);
    } else {
      setShowLoading(false);
    }
    return () => clearTimeout(timeout);
  }, [isLoading]);

  return showLoading;
}
```

#### Timing Thresholds

| Response Time | Behavior |
|---------------|----------|
| < 150ms | No indicator (operation feels instant) |
| 150-300ms | Show indicator after delay |
| > 1000ms | Show progress bar or skeleton |

**Why?** Showing a loading spinner for operations that complete in 50ms creates visual noise and makes the UI feel slower than it is.

### Best Practices for Loading States

| Type | Use Case | Duration |
|------|----------|----------|
| **Skeleton** | Content preview | 1-3 seconds |
| **Progress** | File upload/download | 5+ seconds |
| **Spinner** | Quick operations | < 1 second |
| **Shimmer** | Smooth effect | 1-2 seconds |

---

## ERROR STATES

### Pattern 1: Inline Error (Field-Level)

```jsx
import { Input } from '@/components/ui/input';
import { AlertCircle } from 'lucide-react';

export function EmailInput() {
  const [email, setEmail] = useState('');
  const [error, setError] = useState('');

  const validateEmail = (value) => {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (!value) {
      setError('Email is required');
    } else if (!regex.test(value)) {
      setError('Invalid email format');
    } else {
      setError('');
    }
  };

  return (
    <div>
      <Input
        value={email}
        onChange={(e) => {
          setEmail(e.target.value);
          validateEmail(e.target.value);
        }}
        placeholder="Enter email"
        className={error ? 'border-red-500' : ''}
      />
      {error && (
        <div className="flex items-center gap-2 mt-2 text-red-600 text-sm">
          <AlertCircle size={16} />
          <span>{error}</span>
        </div>
      )}
    </div>
  );
}
```

### Pattern 2: Toast Error (Non-blocking)

```jsx
import { useToast } from '@/components/ui/use-toast';
import { Button } from '@/components/ui/button';

export function DataFetcher() {
  const { toast } = useToast();
  const [isLoading, setIsLoading] = useState(false);

  const fetchData = async () => {
    setIsLoading(true);
    try {
      const response = await fetch('/api/data');
      if (!response.ok) {
        throw new Error('Failed to fetch data');
      }
      const data = await response.json();
      toast({
        title: 'Success',
        description: 'Data loaded successfully',
      });
    } catch (error) {
      toast({
        title: 'Error',
        description: error.message,
        variant: 'destructive',
      });
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <Button onClick={fetchData} disabled={isLoading}>
      {isLoading ? 'Loading...' : 'Fetch Data'}
    </Button>
  );
}
```

### Pattern 3: Alert Dialog (Critical Error)

```jsx
import {
  AlertDialog,
  AlertDialogAction,
  AlertDialogCancel,
  AlertDialogContent,
  AlertDialogDescription,
  AlertDialogTitle,
  AlertDialogTrigger,
} from '@/components/ui/alert-dialog';

export function DeleteConfirmation() {
  const [isOpen, setIsOpen] = useState(false);
  const [error, setError] = useState(null);

  const handleDelete = async () => {
    try {
      await fetch('/api/delete', { method: 'DELETE' });
      setIsOpen(false);
    } catch (err) {
      setError(err.message);
    }
  };

  return (
    <>
      <AlertDialog open={isOpen} onOpenChange={setIsOpen}>
        <AlertDialogTrigger asChild>
          <Button variant="destructive">Delete Item</Button>
        </AlertDialogTrigger>
        <AlertDialogContent>
          <AlertDialogTitle>Are you sure?</AlertDialogTitle>
          <AlertDialogDescription>
            This action cannot be undone.
          </AlertDialogDescription>
          {error && (
            <div className="p-3 bg-red-50 border border-red-200 rounded text-red-800">
              {error}
            </div>
          )}
          <div className="flex gap-2">
            <AlertDialogCancel>Cancel</AlertDialogCancel>
            <AlertDialogAction onClick={handleDelete}>
              Delete
            </AlertDialogAction>
          </div>
        </AlertDialogContent>
      </AlertDialog>
    </>
  );
}
```

### Pattern 4: Error Page (Catastrophic Failure)

```jsx
import { AlertTriangle, Home, RefreshCw } from 'lucide-react';
import { Button } from '@/components/ui/button';

export function ErrorPage({ error, onRetry }) {
  return (
    <div className="min-h-screen flex items-center justify-center p-4">
      <div className="max-w-md text-center">
        <div className="flex justify-center mb-4">
          <AlertTriangle className="w-16 h-16 text-red-600" />
        </div>
        <h1 className="text-2xl font-bold mb-2">Something went wrong</h1>
        <p className="text-gray-600 mb-6">{error}</p>
        <div className="flex gap-4 justify-center">
          <Button onClick={onRetry} variant="default">
            <RefreshCw className="mr-2" size={16} />
            Try again
          </Button>
          <Button onClick={() => window.location.href = '/'} variant="outline">
            <Home className="mr-2" size={16} />
            Go home
          </Button>
        </div>
      </div>
    </div>
  );
}
```

---

## SUCCESS STATES

### Pattern 1: Checkmark Animation

```jsx
import { CheckCircle } from 'lucide-react';
import { useEffect, useState } from 'react';

export function SuccessMessage() {
  const [isVisible, setIsVisible] = useState(true);

  useEffect(() => {
    const timer = setTimeout(() => setIsVisible(false), 3000);
    return () => clearTimeout(timer);
  }, []);

  if (!isVisible) return null;

  return (
    <div className="fixed top-4 right-4 p-4 bg-green-50 border border-green-200 rounded-lg flex items-center gap-3 animate-in fade-in">
      <CheckCircle className="w-6 h-6 text-green-600" />
      <span className="text-green-800">Operation completed successfully!</span>
    </div>
  );
}
```

### Pattern 2: Success Toast

```jsx
import { useToast } from '@/components/ui/use-toast';
import { Button } from '@/components/ui/button';

export function FormSubmit() {
  const { toast } = useToast();
  const [isLoading, setIsLoading] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsLoading(true);

    try {
      await fetch('/api/submit', { method: 'POST' });
      toast({
        title: 'Success!',
        description: 'Your changes have been saved.',
      });
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <Button type="submit" disabled={isLoading}>
        {isLoading ? 'Saving...' : 'Save'}
      </Button>
    </form>
  );
}
```

### Pattern 3: Redirect on Success

```jsx
import { useEffect } from 'react';
import { useNavigate } from 'react-router-dom';

export function LoginForm() {
  const navigate = useNavigate();
  const [isLoading, setIsLoading] = useState(false);

  const handleLogin = async (credentials) => {
    setIsLoading(true);
    try {
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify(credentials),
      });
      const user = await response.json();
      
      // Redirect after successful login
      setTimeout(() => navigate('/dashboard'), 500);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    // Form component
    <div>Login form here</div>
  );
}
```

---

## ANIMATION TIMING

### Standard Durations

```css
/* Fast: Quick feedback (< 200ms) */
--duration-fast: 150ms;
--easing-fast: cubic-bezier(0.4, 0, 0.2, 1);

/* Normal: Standard transitions (200-500ms) */
--duration-normal: 300ms;
--easing-normal: cubic-bezier(0.4, 0, 0.2, 1);

/* Slow: Dramatic entrances (500ms+) */
--duration-slow: 500ms;
--easing-slow: cubic-bezier(0.4, 0, 0.2, 1);
```

### Animation Examples

```jsx
// Fade in
import { useEffect, useState } from 'react';

export function FadeIn({ children }) {
  const [isVisible, setIsVisible] = useState(false);

  useEffect(() => {
    setIsVisible(true);
  }, []);

  return (
    <div
      className={`transition-opacity duration-300 ${
        isVisible ? 'opacity-100' : 'opacity-0'
      }`}
    >
      {children}
    </div>
  );
}

// Scale in
export function ScaleIn({ children }) {
  const [isVisible, setIsVisible] = useState(false);

  useEffect(() => {
    setIsVisible(true);
  }, []);

  return (
    <div
      className={`transition-transform duration-300 ${
        isVisible ? 'scale-100' : 'scale-95'
      }`}
    >
      {children}
    </div>
  );
}
```

---

## ANIMATION PERFORMANCE

### GPU-Accelerated Properties (Preferred)

These properties are composited by the GPU and don't trigger layout recalculation:
- `transform` (translate, scale, rotate)
- `opacity`

### Properties to Avoid Animating

These trigger expensive layout or paint operations:
- `width`, `height` (triggers layout)
- `margin`, `padding` (triggers layout)
- `top`, `left`, `right`, `bottom` (triggers layout)
- `box-shadow` (triggers paint)

### Implementation Examples

```css
/* GOOD: GPU-accelerated */
.animate-slide {
  transform: translateX(0);
  transition: transform 200ms ease-out;
}
.animate-slide.open {
  transform: translateX(-100%);
}

/* AVOID: Layout-triggering */
.animate-slide-bad {
  left: 0;
  transition: left 200ms ease-out;
}
.animate-slide-bad.open {
  left: -100%;
}
```

### CSS-First Animation

Prefer CSS transitions over JavaScript animation libraries for simple state changes:

```tsx
// GOOD: CSS handles the animation
<div className={`transition-opacity duration-300 ${isVisible ? 'opacity-100' : 'opacity-0'}`}>
  Content
</div>

// AVOID: JavaScript animation for simple fade
// (only use JS animation for complex choreography)
```

---

## MICRO-INTERACTIONS

### Hover Effects

```jsx
import { Button } from '@/components/ui/button';

export function HoverButton() {
  return (
    <Button className="hover:scale-105 transition-transform duration-200">
      Hover me
    </Button>
  );
}
```

### Focus Effects

```jsx
import { Input } from '@/components/ui/input';

export function FocusInput() {
  return (
    <Input
      placeholder="Focus on me"
      className="focus:ring-2 focus:ring-blue-500 focus:scale-105 transition-all"
    />
  );
}
```

### Active States

```jsx
import { Button } from '@/components/ui/button';

export function ActiveButton() {
  const [isActive, setIsActive] = useState(false);

  return (
    <Button
      onClick={() => setIsActive(!isActive)}
      className={`transition-colors ${
        isActive ? 'bg-blue-600 text-white' : 'bg-blue-100 text-blue-900'
      }`}
    >
      {isActive ? 'Active' : 'Inactive'}
    </Button>
  );
}
```

---

## KEYBOARD INTERACTIONS

### Keyboard Navigation

```jsx
import { useEffect, useRef } from 'react';

export function KeyboardNavigation({ items }) {
  const [selectedIndex, setSelectedIndex] = useState(0);
  const containerRef = useRef(null);

  useEffect(() => {
    const handleKeyDown = (e) => {
      if (e.key === 'ArrowDown') {
        e.preventDefault();
        setSelectedIndex(prev => 
          Math.min(prev + 1, items.length - 1)
        );
      }
      if (e.key === 'ArrowUp') {
        e.preventDefault();
        setSelectedIndex(prev => Math.max(prev - 1, 0));
      }
    };

    containerRef.current?.addEventListener('keydown', handleKeyDown);
    return () => {
      containerRef.current?.removeEventListener('keydown', handleKeyDown);
    };
  }, [items.length]);

  return (
    <div ref={containerRef} tabIndex={0}>
      {items.map((item, index) => (
        <div
          key={item.id}
          className={`p-4 cursor-pointer ${
            index === selectedIndex ? 'bg-blue-100' : 'bg-white'
          }`}
        >
          {item.name}
        </div>
      ))}
    </div>
  );
}
```

### Enter/Escape Keys

```jsx
import { useEffect, useState } from 'react';

export function Modal() {
  const [isOpen, setIsOpen] = useState(false);

  useEffect(() => {
    const handleKeyDown = (e) => {
      if (e.key === 'Escape') {
        setIsOpen(false);
      }
      if (e.key === 'Enter') {
        // Handle submission
      }
    };

    if (isOpen) {
      window.addEventListener('keydown', handleKeyDown);
      return () => window.removeEventListener('keydown', handleKeyDown);
    }
  }, [isOpen]);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>
      {isOpen && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
          <div className="bg-white p-6 rounded-lg">
            <p>Press Escape to close</p>
          </div>
        </div>
      )}
    </>
  );
}
```

---

## COMPLETE EXAMPLES

### Loading → Success → Auto-dismiss

```jsx
import { useToast } from '@/components/ui/use-toast';
import { Button } from '@/components/ui/button';
import { Spinner } from '@/components/ui/spinner';
import { CheckCircle } from 'lucide-react';
import { useState } from 'react';

export function CompleteFlow() {
  const { toast } = useToast();
  const [state, setState] = useState('idle'); // idle, loading, success, error

  const handleAction = async () => {
    setState('loading');
    try {
      await new Promise(resolve => setTimeout(resolve, 2000));
      setState('success');
      
      // Auto-dismiss after 2 seconds
      setTimeout(() => setState('idle'), 2000);
      
      toast({
        title: 'Success!',
        description: 'Operation completed.',
      });
    } catch (error) {
      setState('error');
      toast({
        title: 'Error',
        description: 'Something went wrong.',
        variant: 'destructive',
      });
    }
  };

  return (
    <div>
      {state === 'idle' && (
        <Button onClick={handleAction}>Start Operation</Button>
      )}
      {state === 'loading' && (
        <div className="flex items-center gap-2">
          <Spinner size="sm" />
          <span>Processing...</span>
        </div>
      )}
      {state === 'success' && (
        <div className="flex items-center gap-2 text-green-600">
          <CheckCircle size={20} />
          <span>Completed!</span>
        </div>
      )}
    </div>
  );
}
```

### Form with Validation Feedback

```jsx
import { useState } from 'react';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { AlertCircle, CheckCircle } from 'lucide-react';

export function ValidatedForm() {
  const [formData, setFormData] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});
  const [isLoading, setIsLoading] = useState(false);
  const [success, setSuccess] = useState(false);

  const validateField = (name, value) => {
    const newErrors = { ...errors };
    
    if (name === 'email') {
      const isValid = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
      if (!isValid) newErrors.email = 'Invalid email';
      else delete newErrors.email;
    }
    
    if (name === 'password') {
      if (value.length < 8) newErrors.password = 'Minimum 8 characters';
      else delete newErrors.password;
    }
    
    setErrors(newErrors);
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    validateField(name, value);
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    setIsLoading(true);
    
    try {
      // API call
      await new Promise(resolve => setTimeout(resolve, 1000));
      setSuccess(true);
      setTimeout(() => setSuccess(false), 3000);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <div>
        <Input
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
          className={errors.email ? 'border-red-500' : ''}
        />
        {errors.email && (
          <p className="text-red-600 text-sm mt-1 flex items-center gap-1">
            <AlertCircle size={14} /> {errors.email}
          </p>
        )}
      </div>

      <div>
        <Input
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
          className={errors.password ? 'border-red-500' : ''}
        />
        {errors.password && (
          <p className="text-red-600 text-sm mt-1 flex items-center gap-1">
            <AlertCircle size={14} /> {errors.password}
          </p>
        )}
      </div>

      {success && (
        <div className="flex items-center gap-2 text-green-600 bg-green-50 p-3 rounded">
          <CheckCircle size={20} />
          <span>Success!</span>
        </div>
      )}

      <Button type="submit" disabled={isLoading || Object.keys(errors).length > 0}>
        {isLoading ? 'Submitting...' : 'Submit'}
      </Button>
    </form>
  );
}
```

---

## SUMMARY

- **Loading:** Use skeleton for content preview, progress for long ops, spinner for quick ops
- **Errors:** Inline for fields, toast for non-blocking, dialog for critical
- **Success:** Checkmark with auto-dismiss, toast notification, or redirect
- **Animations:** Fast (150ms) for feedback, normal (300ms) for transitions, slow (500ms) for entrances
- **Keyboard:** Always support Tab, Enter, Escape in dialogs and forms
- **Duration:** Follow standard timing curves for consistent feel

---

**Next:** [LAYER-07b-PROMPT-PATTERNS.md](LAYER-07b-PROMPT-PATTERNS.md), then [LAYER-08-ACCESSIBILITY.md](LAYER-08-ACCESSIBILITY.md)
