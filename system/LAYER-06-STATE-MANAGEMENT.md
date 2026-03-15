# LAYER-06: STATE MANAGEMENT

**File:** LAYER-06-STATE-MANAGEMENT.md  
**Purpose:** Documented patterns for managing component and application state  
**Audience:** React developers, architects, team leads  
**Last Updated:** January 18, 2026

---

## TABLE OF CONTENTS

1. [Component State Patterns](#component-state-patterns)
2. [Form State Management](#form-state-management)
3. [Application State Patterns](#application-state-patterns)
4. [State Library Comparison](#state-library-comparison)
5. [Data Fetching Patterns](#data-fetching-patterns)
6. [Error Handling](#error-handling)
7. [Context API + Hooks](#context-api--hooks)
8. [Best Practices](#best-practices)

---

## COMPONENT STATE PATTERNS

### Simple State (useState)

Use `useState` for simple, isolated component state that doesn't need to be shared.

```jsx
// ✅ DO: Simple component state
import { useState } from 'react';
import { Button } from '@/components/ui/button';

export function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <Button onClick={() => setCount(count + 1)}>
        Increment
      </Button>
    </div>
  );
}
```

**When to use:**
- Modal open/closed state
- Toggle state (accordion, tabs)
- Input values in simple forms
- Loading states for component-specific async operations
- Animation states

### Reducer for Complex State (useReducer)

Use `useReducer` when multiple pieces of state change together or state transitions are complex.

```jsx
// ✅ DO: Complex state with useReducer
import { useReducer } from 'react';
import { Button } from '@/components/ui/button';

const initialState = {
  items: [],
  loading: false,
  error: null,
};

function reducer(state, action) {
  switch (action.type) {
    case 'FETCH_START':
      return { ...state, loading: true, error: null };
    case 'FETCH_SUCCESS':
      return { ...state, items: action.payload, loading: false };
    case 'FETCH_ERROR':
      return { ...state, error: action.payload, loading: false };
    default:
      return state;
  }
}

export function ItemList() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const fetchItems = async () => {
    dispatch({ type: 'FETCH_START' });
    try {
      const response = await fetch('/api/items');
      const data = await response.json();
      dispatch({ type: 'FETCH_SUCCESS', payload: data });
    } catch (error) {
      dispatch({ type: 'FETCH_ERROR', payload: error.message });
    }
  };

  return (
    <div>
      {state.loading && <p>Loading...</p>}
      {state.error && <p className="text-red-500">{state.error}</p>}
      <ul>
        {state.items.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
      <Button onClick={fetchItems}>Load Items</Button>
    </div>
  );
}
```

**When to use:**
- Multiple related state values
- Complex state transitions
- State that depends on previous state
- Multiple dispatch actions in one flow

### Ref for Non-Rendering Values (useRef)

Use `useRef` for values that don't trigger re-renders.

```jsx
// ✅ DO: useRef for non-rendering values
import { useRef, useCallback } from 'react';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';

export function SearchInput() {
  const inputRef = useRef(null);
  const debounceTimer = useRef(null);

  const handleSearch = useCallback(() => {
    if (debounceTimer.current) {
      clearTimeout(debounceTimer.current);
    }
    
    debounceTimer.current = setTimeout(() => {
      const value = inputRef.current?.value;
      console.log('Searching for:', value);
    }, 300);
  }, []);

  return (
    <div>
      <Input
        ref={inputRef}
        onChange={handleSearch}
        placeholder="Search..."
      />
      <Button onClick={() => inputRef.current?.focus()}>
        Focus Input
      </Button>
    </div>
  );
}
```

**When to use:**
- DOM element references
- Timer IDs
- Previous state values
- Mutable values that shouldn't trigger re-renders

---

## FORM STATE MANAGEMENT

### Uncontrolled Forms (Recommended for Simple Cases)

Use uncontrolled forms when you only need the final values.

```jsx
// ✅ DO: Uncontrolled form with useRef
import { useRef } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';

export function LoginForm() {
  const emailRef = useRef(null);
  const passwordRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    const email = emailRef.current?.value;
    const password = passwordRef.current?.value;
    
    console.log('Login:', { email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <Input ref={emailRef} type="email" placeholder="Email" />
      <Input ref={passwordRef} type="password" placeholder="Password" />
      <Button type="submit">Login</Button>
    </form>
  );
}
```

### Controlled Forms (For Real-Time Validation)

Use controlled forms when you need real-time feedback.

```jsx
// ✅ DO: Controlled form with real-time validation
import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';

export function SignupForm() {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    confirmPassword: '',
  });

  const [errors, setErrors] = useState({});

  const validateEmail = (email) => {
    const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return regex.test(email);
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));

    // Real-time validation
    const newErrors = { ...errors };
    if (name === 'email' && value && !validateEmail(value)) {
      newErrors.email = 'Invalid email format';
    } else {
      delete newErrors[name];
    }
    setErrors(newErrors);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Signup:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <Input
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <p className="text-red-500">{errors.email}</p>}
      </div>
      <Button type="submit">Sign Up</Button>
    </form>
  );
}
```

### Form Libraries (React Hook Form for Complex Forms)

```jsx
// ✅ DO: React Hook Form for complex forms
import { useForm } from 'react-hook-form';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';

export function ComplexForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    defaultValues: {
      email: '',
      password: '',
    }
  });

  const onSubmit = (data) => {
    console.log('Form data:', data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input
        {...register('email', {
          required: 'Email is required',
          pattern: {
            value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
            message: 'Invalid email format'
          }
        })}
        placeholder="Email"
      />
      {errors.email && <p className="text-red-500">{errors.email.message}</p>}
      
      <Input
        {...register('password', {
          required: 'Password is required',
          minLength: { value: 8, message: 'Minimum 8 characters' }
        })}
        type="password"
        placeholder="Password"
      />
      {errors.password && <p className="text-red-500">{errors.password.message}</p>}
      
      <Button type="submit">Submit</Button>
    </form>
  );
}
```

---

## APPLICATION STATE PATTERNS

### Context API + Hooks (For Small to Medium Apps)

**Pattern 1: Simple Context**

```jsx
// ✅ DO: Context for simple app state
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [isDark, setIsDark] = useState(false);

  const toggleTheme = () => setIsDark(!isDark);

  return (
    <ThemeContext.Provider value={{ isDark, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}
```

**Pattern 2: Context with Reducer**

```jsx
// ✅ DO: Context with useReducer for complex state
import { createContext, useContext, useReducer } from 'react';

const AuthContext = createContext();

const initialState = {
  user: null,
  isLoading: false,
  error: null,
};

function authReducer(state, action) {
  switch (action.type) {
    case 'LOGIN_START':
      return { ...state, isLoading: true, error: null };
    case 'LOGIN_SUCCESS':
      return { ...state, user: action.payload, isLoading: false };
    case 'LOGIN_ERROR':
      return { ...state, error: action.payload, isLoading: false };
    case 'LOGOUT':
      return { ...state, user: null };
    default:
      return state;
  }
}

export function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(authReducer, initialState);

  const login = async (email, password) => {
    dispatch({ type: 'LOGIN_START' });
    try {
      // Simulate API call
      const response = await fetch('/api/login', {
        method: 'POST',
        body: JSON.stringify({ email, password }),
      });
      const user = await response.json();
      dispatch({ type: 'LOGIN_SUCCESS', payload: user });
    } catch (error) {
      dispatch({ type: 'LOGIN_ERROR', payload: error.message });
    }
  };

  const logout = () => {
    dispatch({ type: 'LOGOUT' });
  };

  return (
    <AuthContext.Provider value={{ ...state, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}
```

---

## STATE LIBRARY COMPARISON

### Option 1: Jotai (Recommended for New Projects)

**Pros:**
- Atomic state model (primitives compose easily)
- React 19 compatible (works with Server Components)
- Minimal, simple API
- No providers needed
- Excellent TypeScript support
- Bundle size: ~2KB

**Example:**

```jsx
import { atom, useAtom, useAtomValue, useSetAtom } from 'jotai';
import { atomWithStorage } from 'jotai/utils';

// Define atoms (primitive state units)
export const userAtom = atom(null);
export const isLoadingAtom = atom(false);

// Derived atom (computed from other atoms)
export const isAuthenticatedAtom = atom((get) => get(userAtom) !== null);

// Persisted atom (localStorage)
export const themeAtom = atomWithStorage('theme', 'system');

// Usage in component
import { useAtom, useAtomValue } from 'jotai';
import { userAtom, isAuthenticatedAtom } from '@/atoms/auth';

export function UserProfile() {
  const [user, setUser] = useAtom(userAtom);
  const isAuthenticated = useAtomValue(isAuthenticatedAtom);
  
  return (
    <div>
      {isAuthenticated ? <p>Hello, {user.name}</p> : <p>Not logged in</p>}
      <button onClick={() => setUser({ name: 'Test User' })}>
        Login
      </button>
    </div>
  );
}
```

### Option 2: Redux (For Large, Complex Apps)

**Pros:**
- Mature ecosystem
- DevTools for time-travel debugging
- Great for complex state trees
- Large community

**Cons:**
- More boilerplate
- Steeper learning curve
- Bundle size larger (~5KB)

**Use when:**
- App has complex state relationships
- Multiple components need same data
- State changes need to be tracked/audited

### Option 3: Context API (For Simple Apps)

**Pros:**
- Built into React
- No dependencies
- Good for small state

**Cons:**
- Can cause unnecessary re-renders
- Doesn't scale well
- Performance issues with large state trees

**Use when:**
- Simple state (theme, language, auth user)
- Small app
- Don't want dependencies

### Recommendation Matrix

| App Size | Complexity | Recommendation |
|----------|-----------|-----------------|
| Small | Simple | Context API |
| Small | Complex | Jotai |
| Medium | Simple | Jotai or Context API |
| Medium | Complex | Jotai |
| Large | Simple | Jotai |
| Large | Complex | Jotai or Redux |

---

## DATA FETCHING PATTERNS

### Pattern 1: useEffect + useState (Simple)

```jsx
// ✅ DO: Simple data fetching
import { useEffect, useState } from 'react';

export function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      setLoading(true);
      try {
        const response = await fetch('/api/users');
        const data = await response.json();
        setUsers(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []); // Fetch once on mount

  if (loading) return <p>Loading...</p>;
  if (error) return <p className="text-red-500">{error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Pattern 2: Custom Hook (Reusable)

```jsx
// ✅ DO: Custom hook for data fetching
import { useEffect, useState } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isMounted = true;

    const fetchData = async () => {
      setLoading(true);
      try {
        const response = await fetch(url);
        const json = await response.json();
        if (isMounted) {
          setData(json);
        }
      } catch (err) {
        if (isMounted) {
          setError(err.message);
        }
      } finally {
        if (isMounted) {
          setLoading(false);
        }
      }
    };

    fetchData();

    return () => {
      isMounted = false;
    };
  }, [url]);

  return { data, loading, error };
}

// Usage
export function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p className="text-red-500">{error}</p>;

  return <div>{user?.name}</div>;
}
```

### Pattern 3: React Query (Recommended for Large Apps)

```jsx
// ✅ DO: React Query for advanced data fetching
import { useQuery } from '@tanstack/react-query';

async function fetchUsers() {
  const response = await fetch('/api/users');
  return response.json();
}

export function UserList() {
  const { data: users, isLoading, error } = useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
    staleTime: 1000 * 60 * 5, // 5 minutes
    gcTime: 1000 * 60 * 10, // 10 minutes
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p className="text-red-500">{error.message}</p>;

  return (
    <ul>
      {users?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## ERROR HANDLING

### Error Boundaries (For Render Errors)

```jsx
// ✅ DO: Error boundary for render errors
import { Component } from 'react';

export class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="p-4 bg-red-50 border border-red-200">
          <h2 className="text-red-800">Something went wrong</h2>
          <p className="text-red-700">{this.state.error?.message}</p>
        </div>
      );
    }

    return this.props.children;
  }
}
```

### Async Error Handling (For Data Fetching)

```jsx
// ✅ DO: Handle async errors gracefully
import { useState } from 'react';

export function DataFetcher() {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);

  const fetchData = async () => {
    setError(null); // Clear previous errors
    try {
      const response = await fetch('/api/data');
      if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
      }
      const json = await response.json();
      setData(json);
    } catch (err) {
      setError(err.message);
      // Optionally report to error tracking service
      console.error('Fetch error:', err);
    }
  };

  return (
    <div>
      {error && (
        <div className="p-4 bg-red-50 text-red-800">
          Error: {error}
        </div>
      )}
      {data && <p>Data: {JSON.stringify(data)}</p>}
      <button onClick={fetchData}>Fetch Data</button>
    </div>
  );
}
```

---

## CONTEXT API + HOOKS

### Complete Example: Todo Store

```jsx
// store/todoStore.js
import { createContext, useContext, useReducer, useCallback } from 'react';

const TodoContext = createContext();

const initialState = {
  todos: [],
  filter: 'all', // all, active, completed
  isLoading: false,
};

function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
    case 'UPDATE_TODO':
      return {
        ...state,
        todos: state.todos.map(t =>
          t.id === action.payload.id ? action.payload : t
        ),
      };
    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(t => t.id !== action.payload),
      };
    case 'SET_FILTER':
      return {
        ...state,
        filter: action.payload,
      };
    case 'SET_LOADING':
      return {
        ...state,
        isLoading: action.payload,
      };
    default:
      return state;
  }
}

export function TodoProvider({ children }) {
  const [state, dispatch] = useReducer(todoReducer, initialState);

  const addTodo = useCallback((text) => {
    dispatch({
      type: 'ADD_TODO',
      payload: {
        id: Date.now(),
        text,
        completed: false,
      },
    });
  }, []);

  const updateTodo = useCallback((id, updates) => {
    dispatch({
      type: 'UPDATE_TODO',
      payload: { id, ...updates },
    });
  }, []);

  const deleteTodo = useCallback((id) => {
    dispatch({ type: 'DELETE_TODO', payload: id });
  }, []);

  const setFilter = useCallback((filter) => {
    dispatch({ type: 'SET_FILTER', payload: filter });
  }, []);

  const filteredTodos = state.todos.filter(todo => {
    if (state.filter === 'active') return !todo.completed;
    if (state.filter === 'completed') return todo.completed;
    return true;
  });

  const value = {
    todos: filteredTodos,
    allTodos: state.todos,
    filter: state.filter,
    isLoading: state.isLoading,
    addTodo,
    updateTodo,
    deleteTodo,
    setFilter,
  };

  return (
    <TodoContext.Provider value={value}>
      {children}
    </TodoContext.Provider>
  );
}

export function useTodos() {
  const context = useContext(TodoContext);
  if (!context) {
    throw new Error('useTodos must be used within TodoProvider');
  }
  return context;
}
```

---

## BEST PRACTICES

### 1. Keep State as Local as Possible

```jsx
// ✅ DO: Keep state local
function Form() {
  const [email, setEmail] = useState('');
  // Input state doesn't need global context
  return <input value={email} onChange={e => setEmail(e.target.value)} />;
}

// ❌ DON'T: Lift state unnecessarily
// Don't put every input in global state
```

### 2. Avoid State Duplication

```jsx
// ✅ DO: Single source of truth
const [user, setUser] = useState(initialUser);

// ❌ DON'T: Duplicate state
const [user, setUser] = useState(initialUser);
const [userName, setUserName] = useState(user.name); // Don't do this
const [userEmail, setUserEmail] = useState(user.email); // Don't do this
```

### 3. Use Callbacks to Optimize Re-renders

```jsx
// ✅ DO: Memoize callbacks
const handleClick = useCallback(() => {
  dispatch({ type: 'ACTION' });
}, [dispatch]);

// Use with React.memo for child components
const Button = React.memo(({ onClick }) => (
  <button onClick={onClick}>Click me</button>
));
```

### 4. Handle Loading States Explicitly

```jsx
// ✅ DO: Three states (idle, loading, success/error)
const [state, setState] = useState('idle'); // 'idle' | 'loading' | 'success' | 'error'
const [error, setError] = useState(null);
const [data, setData] = useState(null);

// ❌ DON'T: Implicit states
const [loading, setLoading] = useState(false);
// What if you need to show skeletons vs spinners?
```

### 5. Clean Up Side Effects

```jsx
// ✅ DO: Clean up subscriptions/listeners
useEffect(() => {
  const listener = window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);

// ✅ DO: Abort fetch requests
useEffect(() => {
  const controller = new AbortController();
  fetch('/api/data', { signal: controller.signal });
  return () => controller.abort();
}, []);
```

---

## DECISION FLOWCHART

```
Need to manage state?
├─ Is it used by one component only?
│  ├─ YES → useState (simple) or useReducer (complex)
│  └─ NO → Continue
├─ Is it needed by a few components?
│  ├─ YES → useContext + useReducer
│  └─ NO → Continue
├─ Is it needed throughout the app?
│  ├─ Small app (< 5 major features) → useContext + useReducer
│  ├─ Medium app (5-20 features) → Jotai
│  └─ Large app (20+ features) → Jotai or Redux
└─ Is it server data?
   └─ YES → React Query + local state for sync

Form data?
├─ Simple (< 3 fields) → useRef or useState
├─ Complex (3-10 fields) → useState + validation
└─ Very complex (10+ fields) → React Hook Form
```

---

## SUMMARY

- **Component state:** `useState` for simple, `useReducer` for complex
- **Shared state:** Context API for small apps, Jotai for medium/large
- **Server data:** React Query for advanced, useEffect for simple
- **Forms:** Uncontrolled for simple, controlled for real-time feedback, React Hook Form for complex
- **Errors:** Error boundaries for render errors, try/catch for async errors

---

**Next:** [LAYER-07-INTERACTION-PATTERNS.md](LAYER-07-INTERACTION-PATTERNS.md)
