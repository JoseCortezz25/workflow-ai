# Critical Constraints

**Non-negotiable rules that MUST be followed in all code.**

---

## 1. React Server Components (RSC) as architectural foundation

❌ **NEVER**: Use `"use client"` by default or without clear justification  
✅ **ALWAYS**: Start components as Server Components. Only add `"use client"` when browser interactivity, browser APIs, or local state is required

**Correct example**:

```tsx
// app/dashboard/stats.tsx
// ✅ Server Component by default - no "use client"
async function Stats() {
  const data = await fetchStats();
  return <div>{data.total}</div>;
}

// app/dashboard/interactive-chart.tsx
// ✅ Client Component only when necessary
('use client');
import { useState } from 'react';

export function InteractiveChart({ initialData }) {
  const [filter, setFilter] = useState('all');
  // Interactivity logic...
}
```

---

## 2. Server Actions for all mutations

❌ **NEVER**: Make data mutations from client components using direct fetch/axios  
✅ **ALWAYS**: Use Server Actions with explicit session and role validation

**Correct example**:

```tsx
// domains/users/actions.ts
'use server';

export async function updateUserProfile(formData: FormData) {
  // ✅ Mandatory session validation
  const session = await auth();
  if (!session?.user) throw new Error('Unauthorized');

  // ✅ Mandatory role validation
  if (!session.user.roles.includes('admin')) {
    throw new Error('Forbidden');
  }

  // Update logic...
}

// In component
('use client');
import { updateUserProfile } from '@/domains/users/actions';

function ProfileForm() {
  return <form action={updateUserProfile}>...</form>;
}
```

---

## 3. Mandatory Suspense for async operations

❌ **NEVER**: Async components without Suspense boundary  
✅ **ALWAYS**: Wrap components that fetch data with Suspense and appropriate fallback

**Correct example**:

```tsx
// app/dashboard/page.tsx
import { Suspense } from 'react';
import { Stats } from './stats';
import { Skeleton } from '@/components/ui/skeleton';

export default function DashboardPage() {
  return (
    <div>
      {/* ✅ Mandatory Suspense for async components */}
      <Suspense fallback={<Skeleton />}>
        <Stats />
      </Suspense>
    </div>
  );
}
```

---

## 4. Named exports only (NO default exports)

❌ **NEVER**: Use `export default`  
✅ **ALWAYS**: Use named exports for better autocompletion and refactoring

**Correct example**:

```tsx
// ❌ INCORRECT
export default function Button() {}

// ✅ CORRECT
export function Button() {}

// ✅ CORRECT for pages (Next.js allows it)
// app/dashboard/page.tsx
export default function DashboardPage() {} // Exception: Next.js pages
```

---

## 5. Screaming Architecture: Domain-based organization

❌ **NEVER**: Mix business logic in /components or /lib  
✅ **ALWAYS**: Organize business logic in /domains with complete structure per feature

**Correct example**:

```
src/
├── domains/           # ✅ Business logic by domain
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── stores/
│   │   ├── actions.ts
│   │   └── schema.ts
│   ├── users/
│   │   └── ...
│
├── components/        # ✅ Only reusable UI components
│   ├── ui/           # shadcn components
│   ├── atoms/
│   ├── molecules/
│   └── organisms/
```

---

## 6. Strict naming conventions

❌ **NEVER**: Generic names or without semantic prefixes  
✅ **ALWAYS**: Follow specific conventions from official documentation

### Files and Directories

```tsx
// ✅ CORRECT - All files and directories in kebab-case
// Directories
auth-wizard/, user-profile/, data-fetching/

// React Components
login-form.tsx, client-selector.tsx, color-palette-preview.tsx

// Hooks
use-auth.ts, use-extraction.ts, use-client-selection.ts

// Stores (Zustand)
auth.store.ts, extraction.store.ts, ui-state.store.ts

// Schemas (Zod)
login.schema.ts, extraction-config.schema.ts, client.schema.ts

// Types
auth.types.ts, extraction.types.ts, canvas.types.ts

// Services
color.service.ts, okta-auth.service.ts

// Utilities
color.util.ts, serialization.util.ts, format.util.ts

// ❌ INCORRECT
LoginForm.tsx        // PascalCase in filename
useAuth.ts          // camelCase in filename
authStore.ts        // Missing .store suffix
loginSchema.ts      // camelCase without .schema
types.ts            // Too generic
colorUtils.ts       // camelCase instead of .util
```

### Variables and Constants

```tsx
// ✅ Boolean states: MUST use is/has/should/can prefix
const isLoading = true;
const isAuthenticated = true;
const isVisible = true;
const hasError = false;
const hasSelection = true;
const hasChildren = false;
const shouldRedirect = true;
const shouldRefetch = false;
const canExtract = true;
const canSubmit = false;

// ✅ Non-boolean variables: descriptive camelCase
const selectedClient = client;
const extractedColors = colors;
const currentUser = user;
const tokenExpirationTime = 3600;

// ✅ Global constants: SCREAMING_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com';
const MAX_EXTRACTION_NODES = 100;
const DEFAULT_TIMEOUT_MS = 5000;

// ❌ INCORRECT
const loading = true; // Missing "is" prefix
const error = false; // Missing "has" prefix
const redirect = true; // Missing "should" prefix
const SelectedClient = client; // PascalCase instead of camelCase
const extracted_colors = colors; // snake_case instead of camelCase
const apiBaseUrl = 'url'; // camelCase for global constant
```

### Functions and Methods

```tsx
// ✅ Event handlers: handle prefix
const handleLogin = () => {};
const handleClientSelection = () => {};
const handleFormSubmit = () => {};

// ✅ Fetch/Request functions: fetch/get/load + resource
const fetchClients = async () => {};
const getAuthToken = () => {};
const loadUserProfile = async () => {};

// ✅ Transformation functions: Verb + Object
const parseFrameworkData = data => {};
const transformDataToComponent = data => {};
const formatColorToHex = color => {};

// ❌ INCORRECT
const onLogin = () => {}; // Use "handle" not "on" for implementation
const login = () => {}; // Missing "handle" prefix
const clients = async () => {}; // Missing "fetch/get/load" prefix
const frameworkParser = data => {}; // Noun instead of Verb + Object
```

### React Components and Props

```tsx
// ✅ Component names: PascalCase
export function LoginForm() {}
export function ClientSelector() {}
export function ColorPalettePreview() {}

// ✅ Component Props: {ComponentName}Props
interface LoginFormProps {
  onSubmit: (credentials: Credentials) => void;
  isLoading: boolean;
  errorMessage?: string;
}

export function LoginForm({
  onSubmit,
  isLoading,
  errorMessage
}: LoginFormProps) {
  // Implementation
}

// ❌ INCORRECT
export function loginForm() {} // camelCase instead of PascalCase
export default function LoginForm() {} // Default export (use named exports)
interface Props {} // Too generic
interface ILoginFormProps {} // Avoid "I" prefix
```

### Types and Schemas

```tsx
// ✅ Interfaces and Types: PascalCase (no I prefix)
interface User {}
interface ExtractionResult {}
type MessageType = 'info' | 'error' | 'success';

// ✅ Zod schemas: camelCase with Schema suffix
export const loginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8)
});

// ✅ Inferred types from schemas: PascalCase (no additional suffix)
export type Login = z.infer<typeof loginSchema>;

// ❌ INCORRECT
interface IUser {} // Avoid "I" prefix
interface UserInterface {} // Avoid "Interface" suffix
export const LoginSchema = z.object({}); // PascalCase instead of camelCase
export type LoginData = z.infer<typeof loginSchema>; // Unnecessary suffix
export type LoginType = z.infer<typeof loginSchema>; // Unnecessary suffix
```

---

## 7. Text Management: Domain-centralized messages

❌ **NEVER**: Hardcode text strings directly in components  
✅ **ALWAYS**: Use centralized messages from `/config/messages.ts` (global) or `/domains/{domain}/messages.ts` (domain-specific)

### Global Messages

```tsx
// ✅ CORRECT: /config/messages.ts
export const messages = {
  common: {
    actions: {
      save: 'Save',
      cancel: 'Cancel',
      delete: 'Delete',
      edit: 'Edit',
      create: 'Create',
      search: 'Search',
      filter: 'Filter',
      export: 'Export',
      import: 'Import'
    },
    states: {
      loading: 'Loading...',
      error: 'An error occurred',
      success: 'Success'
    }
  },
  validation: {
    required: 'This field is required',
    email: 'Enter a valid email',
    minLength: 'Must have at least {min} characters'
  }
} as const;

export type Messages = typeof messages;
```

### Domain Messages

```tsx
// ✅ CORRECT: /domains/auth/messages.ts
export const authMessages = {
  login: {
    title: 'Sign In',
    submitButton: 'Login',
    emailLabel: 'Email',
    passwordLabel: 'Password',
    forgotPassword: 'Forgot Password?',
    rememberMe: 'Remember me'
  },
  validation: {
    emailInvalid: 'Please enter a valid email',
    passwordTooShort: 'Password must be at least 8 characters',
    loginFailed: 'Invalid credentials'
  }
} as const;

// ✅ Use in component
// domains/auth/components/molecules/login-button.tsx
import { authMessages } from '../../messages';

export function LoginButton() {
  return <button>{authMessages.login.submitButton}</button>;
}
```

### Dynamic Messages

```tsx
// ✅ CORRECT: Functions for dynamic text
// domains/users/messages.ts
export const userMessages = {
  greeting: (name: string) => `Welcome, ${name}!`,
  itemCount: (count: number) => `${count} ${count === 1 ? 'item' : 'items'}`
} as const;

// ✅ Use in component
import { userMessages } from '../messages';

export function UserGreeting({ name }: { name: string }) {
  return <h1>{userMessages.greeting(name)}</h1>;
}
```

### When to Use Each

**Use `/config/messages.ts` when**:

- Text is used across multiple domains (Save, Cancel, Delete, etc.)
- Generic system states (Loading, Error, Success)
- Common validation messages (Required field, Invalid email)
- Shared navigation elements

**Use `/domains/{domain}/messages.ts` when**:

- Text is specific to that domain only
- Domain-specific form labels
- Domain-specific error messages
- Domain-specific titles and descriptions

### Best Practices

```tsx
// ✅ CORRECT
import { messages } from '@/config/messages';
import { authMessages } from '@/domains/auth/messages';

export function LoginForm() {
  return (
    <>
      <button>{messages.common.actions.save}</button>
      <h1>{authMessages.login.title}</h1>
    </>
  );
}

// ❌ INCORRECT: Hardcoded strings
export function LoginForm() {
  return (
    <>
      <button>Save</button>
      <h1>Sign In</h1>
    </>
  );
}
```

**Critical Rules**:

1. One `messages.ts` file per domain (no fragmentation)
2. Always use `as const` for type safety
3. Export types for autocomplete: `export type Messages = typeof messages;`
4. Use functions for dynamic text (not string concatenation)
5. Use descriptive nested objects: `login.title`, `login.submitButton`

---

## 8. State Management Strategy: Right tool for the right job

❌ **NEVER**: Use Zustand for server state (backend data) or useState for complex forms  
✅ **ALWAYS**: Follow the state management decision matrix based on data type

### Decision Matrix

| State Type    | Tool            | When to Use                         | Example                        |
| ------------- | --------------- | ----------------------------------- | ------------------------------ |
| **Server**    | React Query     | Data from backend (fetched, cached) | User list, workouts, exercises |
| **Client/UI** | Zustand         | UI state, local preferences         | Sidebar open, theme, filters   |
| **URL**       | nuqs            | Shareable state in URL params       | Search filters, pagination     |
| **Local**     | useState        | Component-only state                | Form input, modal open         |
| **Forms**     | React Hook Form | Complex forms with validation       | Multi-step forms, registration |

### ❌ WRONG: Zustand for Server State

```tsx
// DON'T DO THIS
import { create } from 'zustand';

const useWorkoutStore = create(set => ({
  workouts: [],
  loading: false,
  fetchWorkouts: async () => {
    set({ loading: true });
    const data = await api.getWorkouts();
    set({ workouts: data, loading: false });
  }
}));
```

**Why it's wrong**:

- Manual loading state management
- No automatic cache invalidation
- No optimistic updates
- Hard to handle error states
- Duplicates data across components

### ✅ CORRECT: React Query for Server State

```tsx
// DO THIS
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { workoutRepository } from '@/domains/workouts/actions';

export function useWorkouts() {
  return useQuery({
    queryKey: ['workouts'],
    queryFn: () => workoutRepository.findAll()
  });
}

export function useCreateWorkout() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: workoutRepository.create,
    onSuccess: () => {
      // ✅ Automatic cache invalidation
      queryClient.invalidateQueries({ queryKey: ['workouts'] });
    }
  });
}

// In component
function WorkoutList() {
  const { data: workouts, isLoading, error } = useWorkouts();
  const createWorkout = useCreateWorkout();

  // ✅ React Query handles loading, error, cache automatically
}
```

### ✅ CORRECT: Zustand for Client/UI State

```tsx
// DO THIS
import { create } from 'zustand';

// ✅ Only UI/client state
export const useUIStore = create(set => ({
  sidebarOpen: true,
  theme: 'light',
  toggleSidebar: () => set(state => ({ sidebarOpen: !state.sidebarOpen })),
  setTheme: theme => set({ theme })
}));

// In component
function Sidebar() {
  const { sidebarOpen, toggleSidebar } = useUIStore();
  // ✅ Perfect for UI state
}
```

### ✅ CORRECT: nuqs for URL State (Shareable State)

```tsx
// DO THIS
import { useQueryState, parseAsString, parseAsInteger } from 'nuqs';

function ProductList() {
  // ✅ State in URL params - shareable and persistent
  const [search, setSearch] = useQueryState('search', parseAsString);
  const [page, setPage] = useQueryState('page', parseAsInteger.withDefault(1));

  // URL: /products?search=laptop&page=2
  // ✅ Users can share this URL and maintain state

  return (
    <>
      <input value={search || ''} onChange={e => setSearch(e.target.value)} />
      <Pagination page={page} onPageChange={setPage} />
    </>
  );
}
```

**When to use nuqs**:

- Search filters that should be shareable
- Pagination state
- Sort/filter options
- Tab selections that should persist in URL
- Any state that benefits from being in the URL for shareability

### ✅ CORRECT: useState for Local State

```tsx
// DO THIS
function SearchBar() {
  const [query, setQuery] = useState(''); // ✅ Local to this component

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

### ✅ CORRECT: React Hook Form for Complex Forms

```tsx
// DO THIS
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { registerSchema } from './schema';

function RegisterForm() {
  const {
    register,
    handleSubmit,
    formState: { errors }
  } = useForm({
    resolver: zodResolver(registerSchema)
  });

  // ✅ Handles validation, errors, submission state
}
```

---

## 9. Middleware + Server Actions for route protection

❌ **NEVER**: Validate authentication only on client-side  
✅ **ALWAYS**: 3-layer validation: Middleware → Server Action → Client UI

**Correct example**:

```tsx
// middleware.ts
// ✅ Layer 1: Middleware intercepts route
export async function middleware(request) {
  const session = await auth();
  if (!session) return NextResponse.redirect('/login');

  // Validate roles
  if (request.nextUrl.pathname.startsWith('/admin')) {
    if (!session.user.roles.includes('admin')) {
      return NextResponse.redirect('/unauthorized');
    }
  }
}

// domains/admin/actions.ts
// ✅ Layer 2: Server Action validates again
('use server');
export async function deleteUser(id: string) {
  const session = await auth();
  if (!session?.user.roles.includes('admin')) {
    throw new Error('Unauthorized');
  }
  // ...
}

// ✅ Layer 3: Conditional Client UI
('use client');
function AdminPanel() {
  const user = useAuthStore(s => s.user);

  if (!user.roles.includes('admin')) {
    return null; // Hide sensitive UI
  }

  return <button onClick={() => deleteUser(id)}>Delete</button>;
}
```

---

## 10. Forms: React Hook Form + Zod for validation

❌ **NEVER**: Handle complex form state manually with useState  
✅ **ALWAYS**: Use React Hook Form with Zod validation for all forms

**Correct example**:

```tsx
// domains/auth/components/register-form.tsx
'use client';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { registerSchema } from '../schema';

export function RegisterForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting }
  } = useForm({
    resolver: zodResolver(registerSchema)
  });

  const onSubmit = async data => {
    // Submit logic with validated data
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} type="email" />
      {errors.email && <span>{errors.email.message}</span>}

      <input {...register('password')} type="password" />
      {errors.password && <span>{errors.password.message}</span>}

      <button disabled={isSubmitting}>
        {isSubmitting ? 'Submitting...' : 'Register'}
      </button>
    </form>
  );
}
```

**Why React Hook Form**:

- Built-in validation with Zod integration
- Automatic error handling
- Performance optimized (minimal re-renders)
- Type-safe form data
- Built-in loading states

---

## 11. Styles: Tailwind + @apply for repetition

❌ **NEVER**: Long repeated class strings or arbitrary inline styles  
✅ **ALWAYS**: Use @apply in CSS files for repeated patterns, maintain mobile-first and using BEM for class names.

**Correct example**:

```css
/* styles/components/atoms/input.css */
/* ✅ Extract repeated patterns with @apply */
.input-base {
  @apply rounded-md border border-gray-300 px-3 py-2 focus:ring-2 focus:ring-blue-500 focus:outline-none;
}

.input-error {
  @apply input-base border-red-500 focus:ring-red-500;
}
```

```tsx
export function Input({ error }) {
  return <input className={error ? 'input-error' : 'input-base'} />;
}

// ✅ Mobile-first always
<div className="w-full md:w-1/2 lg:w-1/3">
  {/* Start with mobile (w-full), then tablets and desktop */}
</div>;
```

---

## 12. Business logic in custom hooks

❌ **NEVER**: Place business logic directly in components or duplicate logic across components  
✅ **ALWAYS**: Extract business logic to custom hooks within the corresponding domain

**Correct example**:

```tsx
// domains/workouts/hooks/use-workout-stats.ts
// ✅ Business logic encapsulated in custom hook
import { useState, useEffect } from 'react';
import { fetchWorkoutStats } from '../actions';

export function useWorkoutStats(userId: string) {
  const [stats, setStats] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function loadStats() {
      try {
        setIsLoading(true);
        const data = await fetchWorkoutStats(userId);
        setStats(data);
      } catch (err) {
        setError(err);
      } finally {
        setIsLoading(false);
      }
    }
    loadStats();
  }, [userId]);

  return { stats, isLoading, error };
}

// domains/workouts/components/workout-dashboard.tsx
('use client');
import { useWorkoutStats } from '../hooks/use-workout-stats';

export function WorkoutDashboard({ userId }: { userId: string }) {
  // ✅ Clean component: delegates logic to custom hook
  const { stats, isLoading, error } = useWorkoutStats(userId);

  if (isLoading) return <Spinner />;
  if (error) return <Error message={error.message} />;

  return <StatsDisplay data={stats} />;
}
```

**Incorrect example**:

```tsx
// ❌ INCORRECT: Business logic mixed in component
'use client';
import { useState, useEffect } from 'react';

export function WorkoutDashboard({ userId }: { userId: string }) {
  const [stats, setStats] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  // ❌ Business logic directly in component
  useEffect(() => {
    fetch(`/api/workouts/${userId}/stats`)
      .then(res => res.json())
      .then(data => {
        // ❌ Complex calculations in component
        const processed = {
          total: data.workouts.length,
          avgDuration:
            data.workouts.reduce((acc, w) => acc + w.duration, 0) /
            data.workouts.length
          // ... more logic
        };
        setStats(processed);
        setIsLoading(false);
      });
  }, [userId]);

  return <div>{stats?.total}</div>;
}
```

## Verification Checklist for Agents

Before proceeding with any task, verify:

- [ ] I have read this entire document
- [ ] I understand all critical rules
- [ ] I will follow these rules in all code I plan or review
- [ ] I will flag violations of these rules if I find them
- [ ] If any rule is unclear, I will ask for clarification before proceeding

### Component & Architecture

- [ ] New component? → Check if it should be RSC (default) or needs `"use client"`
- [ ] Data mutation? → Must use Server Action with session validation
- [ ] Async fetch? → Must be wrapped in `<Suspense>`
- [ ] Exports? → Must be named exports (no default, except Next.js pages)
- [ ] Business logic? → Must be in `/domains/{domain}/` and extracted to custom hooks
- [ ] Protected route? → Middleware + Server Action + Client UI validation

### Naming Conventions

- [ ] Files? → All files in `kebab-case.tsx` (not PascalCase or camelCase)
- [ ] Directories? → All directories in `kebab-case/` (not PascalCase or snake_case)
- [ ] Component names? → PascalCase in code: `export function LoginForm()`
- [ ] Component props? → `{ComponentName}Props` interface
- [ ] Hooks? → `use-{name}.ts` filename, exported as `useHookName`
- [ ] Stores? → `{name}.store.ts` filename
- [ ] Schemas? → `{name}.schema.ts` filename, variable as `nameSchema`
- [ ] Types? → `{name}.types.ts` filename, PascalCase names (no `I` prefix)
- [ ] Services? → `{name}.service.ts` filename
- [ ] Utilities? → `{name}.util.ts` filename
- [ ] Boolean variables? → Must use `is/has/should/can` prefix
- [ ] Non-boolean variables? → Descriptive camelCase without prefixes
- [ ] Constants? → `SCREAMING_SNAKE_CASE` for global constants
- [ ] Event handlers? → `handle` prefix (not `on`)
- [ ] Fetch functions? → `fetch/get/load` + resource name
- [ ] Transform functions? → Verb + Object structure

### Text Management

- [ ] Text strings? → Never hardcoded, use `/config/messages.ts` or `/domains/{domain}/messages.ts`
- [ ] Global text? → Use `/config/messages.ts` (Save, Cancel, Loading, etc.)
- [ ] Domain text? → Use `/domains/{domain}/messages.ts` (domain-specific labels)
- [ ] Dynamic text? → Use functions, not string concatenation
- [ ] Messages file? → Always use `as const` for type safety

### State Management

- [ ] State management? → Use correct tool: React Query (server), Zustand (UI), nuqs (URL), useState (local), React Hook Form (forms)
- [ ] Backend data? → Must use React Query for fetching/caching, never Zustand
- [ ] UI/Client state? → Zustand store in `/domains/{domain}/stores/`
- [ ] URL state? → nuqs for shareable filters, pagination, etc.
- [ ] Local state? → useState for component-only state
- [ ] Forms? → React Hook Form with Zod validation (zodResolver)

### Styles

- [ ] Repeated styles? → Extract to @apply in appropriate CSS files
- [ ] Mobile-first? → Always start with mobile, then tablet/desktop breakpoints
- [ ] Class names? → Use BEM for custom classes
