# File Structure

**Naming conventions and file organization patterns for Osborn Frontend.**

**Purpose**: Ensure consistent file naming, directory structure, and import patterns across the project.

---

## How to Use This Document

This document defines Osborn's conventions for:

1. **File Naming**: How to name different types of files
2. **Directory Structure**: Where files should be located (Screaming Architecture + Atomic Design)
3. **Import Patterns**: How to import modules (absolute paths preferred)
4. **Grouping Strategy**: Feature-based organization with domain encapsulation

**Token Size**: ~500 tokens

**Agents**: Read this when creating new files or organizing code

---

## File Naming Conventions

**All files and directories use kebab-case** for consistency and cross-platform compatibility.

| Type                 | Convention                   | Example                         | Location                        |
| -------------------- | ---------------------------- | ------------------------------- | ------------------------------- |
| **Directories**      | kebab-case                   | `user-profile/`, `auth-wizard/` | All directories                 |
| **React Components** | kebab-case.tsx               | `login-form.tsx`                | `/components` or `/domains`     |
| **Hooks**            | use-{name}.ts                | `use-login-submit.ts`           | `/hooks` (global or per domain) |
| **Stores (Zustand)** | {name}.store.ts              | `auth.store.ts`                 | `/stores` (per domain)          |
| **Schemas (Zod)**    | {name}.schema.ts             | `login.schema.ts`               | Root of domain or `/lib`        |
| **Types**            | {name}.types.ts              | `auth.types.ts`                 | `/lib/types.ts` or per domain   |
| **Services**         | {name}.service.ts            | `okta-auth.service.ts`          | Per domain or `/lib`            |
| **Utilities**        | {name}.util.ts               | `format-date.ts`                | `/utils`                        |
| **Messages**         | messages.ts                  | `messages.ts`                   | `/config` or per domain         |
| **Styles**           | {component-name}.css         | `login-form.css`                | `/styles` (mirrors structure)   |
| **Stories**          | {component-name}.stories.tsx | `button.stories.tsx`            | `/stories` (mirrors structure)  |

---

## Directory Structure

**Osborn uses Screaming Architecture + Atomic Design** for a feature-based, domain-driven organization.

### Official Project Structure

```
/src
├── app/                            # App Router (layout, routes, pages)
│   ├── layout.tsx
│   ├── page.tsx
│   ├── dashboard/
│   │   ├── page.tsx
│   │   └── loading.tsx
│   └── api/
│       └── auth/route.ts
│
├── components/                     # Global UI components
│   ├── ui/                         # shadcn components copied via CLI
│   ├── atoms/                      # Simple shared visual components
│   ├── molecules/                  # Composition of atoms
│   ├── organisms/                  # Composition of molecules
│   └── layout/                     # Common layouts (Navbar, Sidebar, Footer)
│
├── domains/                        # Business domains (Screaming Architecture)
│   ├── auth/
│   │   ├── components/             # Domain-specific components
│   │   │   ├── atoms/
│   │   │   └── molecules/
│   │   ├── hooks/
│   │   ├── stores/                 # Zustand stores for the auth domain
│   │   │   └── auth.store.ts
│   │   ├── messages.ts             # Auth domain text messages
│   │   ├── validation-messages.ts  # Auth validation messages
│   │   ├── auth.schema.ts
│   │   └── actions.ts
│   │
│   ├── users/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── stores/
│   │   │   └── user.store.ts
│   │   ├── messages.ts             # Users domain text messages
│   │   │                           # For i18n: use messages/en.ts, messages/es.ts
│   │   └── ...
│
├── lib/                            # Global logic and initializations
│   ├── auth.ts
│   ├── db.ts
│   └── middleware.ts
│
├── config/                         # Global configurations
│   ├── site.ts                     # Titles, metadata, etc.
│   ├── messages.ts                 # Common/shared UI texts (cross-domain)
│   └── constants.ts                # Global constants
│
├── styles/
│   ├── main.css                    # Global styles: resets, tailwind base, etc.
│   ├── components/                 # Reusable global styles (not tied to a domain)
│   │   ├── atoms/
│   │   │   └── input.css
│   │   ├── molecules/
│   │   │   └── form-group.css
│   │   ├── organisms/
│   │   │   └── hero-section.css
│   │   └── layout/
│   │       ├── header.css
│   │       └── sidebar.css
│   │
│   ├── domains/                    # Domain-specific styles (Screaming Architecture)
│   │   ├── auth/
│   │   │   └── login-form.css
│   │   ├── users/
│   │   │   └── user-card.css
│   │   └── ...
│   │
│   └── utils/                      # Mixins, helpers, media queries, etc.
│       ├── media.css
│       └── animations.css
│
├── utils/                          # Shared utility functions
│   ├── date.util.ts
│   ├── validation.util.ts
│   └── ...
│
├── stories/                        # Visual components for Storybook
│   ├── components/
│   │   ├── button.stories.tsx
│   │   └── card.stories.tsx
│   ├── domains/
│   │   ├── auth/
│   │   │   └── login-form.stories.tsx
│   │   └── users/
│   │       └── user-card.stories.tsx
│   └── README.md
│
├── .storybook/                     # Storybook configuration
│   ├── main.ts
│   └── preview.ts
│
├── .env                            # Environment variables
└── middleware.ts                   # Global Next.js middleware
```

### Key Organizational Principles

1. **Screaming Architecture**: Business domains are first-class citizens in `/domains`
2. **Atomic Design**: Global components organized by complexity (atoms → molecules → organisms)
3. **Domain Encapsulation**: Each domain contains its own components, hooks, stores, schemas, messages, and actions
4. **Layout Components**: Common structural components (Navbar, Sidebar, Footer) in `/components/layout`
5. **Style Mirroring**: `/styles` mirrors both component and domain structure
6. **Story Mirroring**: `/stories` mirrors the component structure for Storybook
7. **Next.js App Router**: File-system based routing in `/app`
8. **Domain Messages**: Each domain has its own `messages.ts` for domain-specific text (i18n ready)

### Structure Details

**`/app` folder**: Houses main routes and pages following Next.js App Router file-system routing. Contains general layouts, main pages, and API routes.

**`/components` folder**: Contains globally reusable components structured by Atomic Design. `/ui` groups shadcn/ui components, while `/atoms`, `/molecules`, and `/organisms` organize reusable visual elements. `/layout` groups structural components.

**`/domains` directory**: Organizes business logic following Screaming Architecture. Each domain encapsulates its own:

- Components (organized by Atomic Design within the domain)
- Hooks
- Services
- Validations
- Actions
- Stores (Zustand for domain state)
- Messages (domain-specific UI texts)

**`/styles` folder**: Defines visual foundation organized by levels of abstraction (Atomic Design) and business contexts. Keeps reusable styles, domain-specific styles, and global configurations separate.

**`/lib` folder**: Centralizes initializations and shared logic between domains (auth configuration, database access, middlewares).

**`/config` folder**: Global system configurations including app metadata (`site.ts`), global constants, and common messages shared across domains (`messages.ts`). Domain-specific messages stay within domain folders.

**`/utils` folder**: Generic utility functions (formatters, validators) used across multiple modules.

**`/stories` directory**: Storybook stories for component documentation. Structured to separate global components from domain-specific ones, maintaining consistency with project architecture.

**`.storybook` folder**: Storybook configuration files (`main.ts`, `preview.ts`).

---

## Import Patterns

### Import Path Strategy

**Osborn uses absolute imports with the `@/` alias** for all cross-module imports.

```typescript
// tsconfig.json or vite.config.ts - Configure aliases
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"],
      "@/features/*": ["./src/features/*"],
      "@/shared/*": ["./src/shared/*"],
      "@/core/*": ["./src/core/*"]
    }
  }
}
```

**✅ PREFERRED: Absolute Imports**

```typescript
// Import from domains
import { useLoginSubmit } from '@/domains/auth/hooks/use-login-submit';
import { useAuthStore } from '@/domains/auth/stores/auth.store';
import type { LoginFormData } from '@/domains/auth/login.schema';

// Import global components
import { Button } from '@/components/ui/button';
import { Input } from '@/components/organisms/inputs/input';
import { Checkbox } from '@/components/atoms/checkbox/checkbox';

// Import config & lib
import { messages } from '@/config/messages';
import { siteConfig } from '@/config/site';
import { cn } from '@/lib/utils';

// Import utilities
import { formatDate } from '@/utils/format-date';
```

**❌ AVOID: Relative Imports for Distant Files**

```typescript
// Hard to read and maintain
import { Module } from '../../../../path/to/module.file';
import { Component } from '../../../other/path/Component';
```

---

### Barrel Files (index.ts)

**Osborn Preference: ❌ NO Barrel Files**

```typescript
// ❌ DON'T create index.ts files that re-export

// ✅ CORRECT: Direct imports
import { Button } from '@/components/ui/button';
import { Input } from '@/components/organisms/inputs/input';
import { useLoginSubmit } from '@/domains/auth/hooks/use-login-submit';
import { formatDate } from '@/utils/format-date';
```

**Why**:

- Better tree-shaking (smaller bundle sizes)
- Faster builds
- Clearer dependencies
- Easier to track where things are imported from

**Exception**: Utility barrel at `/utils/index.ts` for commonly used utilities

```typescript
// utils/index.ts - ONLY for utilities
export { formatDate } from './format-date';
export { validateEmail } from './validate-email';

// Usage
import { formatDate, validateEmail } from '@/utils';
```

---

### Import Ordering

**Osborn follows a 4-group strategy** (enforced by Prettier with `prettier-plugin-tailwindcss`):

1. **External packages** (React, Next.js, third-party libraries)
2. **Internal absolute imports** (`@/*` imports)
3. **Relative imports** (`./ `and `../`)
4. **Type imports** (if separated)
5. **Styles** (CSS imports, if any)

```typescript
// 1. External packages
import { useState } from 'react';
import { useRouter } from 'next/navigation';
import { create } from 'zustand';

// 2. Internal absolute imports
import { messages } from '@/config/messages';
import { Button } from '@/components/ui/button';
import { useAuthStore } from '@/domains/auth/stores/auth.store';
import { formatDate } from '@/utils/format-date';

// 3. Relative imports
import { SubComponent } from './sub-component';
import { useLocalHook } from '../hooks/use-local-hook';

// 4. Types (if using separate type imports)
import type { User } from '@/lib/types';
import type { LoginFormData } from '../login.schema';

// 5. Styles (rare, as Tailwind is preferred)
import './component.css';
```

**Tools**: Prettier + `prettier-plugin-tailwindcss` automatically sort imports.

---

## Grouping Strategy

**Osborn uses Hybrid: Feature-Based (Domains) + Type-Based (Global Components)**

### Domain-Based Organization (Primary)

**Location**: `/domains`

**Purpose**: Encapsulate business logic by feature/context

**Structure**:

```
domains/
├── auth/           # Everything auth-related
│   ├── components/
│   ├── hooks/
│   ├── stores/
│   └── schemas/
└── invitations/    # Everything invitations-related
    ├── components/
    └── schemas/
```

**Advantages**:

- Related code lives together
- Easy to understand feature scope
- Can delete entire feature without affecting others
- Clear boundaries between features

---

### Type-Based Organization (Secondary)

**Location**: `/components`

**Purpose**: Reusable UI components (cross-domain)

**Structure**:

```
components/
├── atoms/          # Simple components (buttons, inputs)
├── molecules/      # Composed components (forms, cards)
├── organisms/      # Complex components (tables, modals)
└── ui/             # shadcn/ui base components
```

**Use When**:

- Component is used across multiple domains
- Component has no business logic
- Component is purely presentational

---

## Test Files

**Osborn uses Vitest** for testing with **colocated test files**.

**Naming Convention**: `{filename}.test.ts` or `{filename}.test.tsx`

**Location**: Next to the source file

```
components/
├── atoms/
│   └── button/
│       ├── button.tsx
│       └── button.test.tsx       ← Colocated
domains/
└── auth/
    ├── hooks/
    │   ├── use-login-submit.ts
    │   └── use-login-submit.test.ts   ← Colocated
    └── services.ts
        └── services.test.ts            ← Colocated
```

**Why Colocated**:

- Tests are easy to find
- Tests move/delete with source code
- Clear what is tested

**Exception**: Integration/E2E tests may live in separate `/tests` folder (not currently implemented)

---

## Special File Types

### Message Files (messages.ts)

**Location**:

- `/config/messages.ts` for global/common messages
- `/domains/{domain}/messages.ts` for domain-specific messages

**Naming**: `messages.ts`

**Structure**:

```typescript
// config/messages.ts or domains/{domain}/messages.ts
export const messages = {
  common: {
    loading: 'Loading...',
    save: 'Save',
    cancel: 'Cancel'
  },
  validation: {
    required: 'This field is required',
    email: 'Enter a valid email'
  }
} as const;

export type Messages = typeof messages;
```

---

### Zod Schemas ({name}.schema.ts)

**Location**:

- `/lib/schema.ts` for shared schemas
- `/domains/{domain}/{name}.schema.ts` for domain-specific schemas

**Naming**: `{entity}.schema.ts` (e.g., `login.schema.ts`)

**Structure**:

```typescript
// domains/auth/login.schema.ts
import { z } from 'zod';

export const loginSchema = z.object({
  email: z.string().email('Enter a valid email'),
  password: z.string().min(8, 'Password must be at least 8 characters')
});

export type LoginFormData = z.infer<typeof loginSchema>;
```

---

### Zustand Stores ({name}.store.ts)

**Location**: `/domains/{domain}/stores/{name}.store.ts`

**Naming**: `{name}.store.ts` (e.g., `auth.store.ts`)

**Structure**:

```typescript
// domains/auth/stores/auth.store.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface AuthState {
  token: string;
  profile: User | null;
}

interface AuthActions {
  setToken: (token: string) => void;
  setProfile: (profile: User) => void;
}

export const useAuthStore = create<AuthState & AuthActions>()(
  persist(
    set => ({
      token: '',
      profile: null,
      setToken: token => set({ token }),
      setProfile: profile => set({ profile })
    }),
    { name: 'auth' }
  )
);
```

---

### Custom Hooks (use-{name}.ts)

**Location**:

- `/hook` for global hooks
- `/domains/{domain}/hooks/` for domain-specific hooks

**Naming**: `use-{functionality}.ts` (e.g., `use-login-submit.ts`)

**Structure**:

```typescript
// domains/auth/hooks/use-login-submit.ts
import { useState } from 'react';

interface UseLoginSubmitOptions {
  onSuccess?: () => void;
  onError?: (error: string) => void;
}

export const useLoginSubmit = (options?: UseLoginSubmitOptions) => {
  const [isLoading, setIsLoading] = useState(false);

  const onSubmit = async (data: LoginFormData) => {
    // Implementation
  };

  return { isLoading, onSubmit };
};
```

---

### Storybook Stories ({name}.stories.tsx)

**Location**: `/stories` (mirrors component structure)

**Naming**: `{component-name}.stories.tsx`

**Structure**:

```typescript
// stories/components/atoms/button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { Button } from '@/components/ui/button';

const meta: Meta<typeof Button> = {
  title: 'Components/Atoms/Button',
  component: Button,
  tags: ['autodocs']
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  args: {
    children: 'Button',
    variant: 'default'
  }
};
```

---

### Configuration Files

**Root Level**:

```
project-root/
├── .env                        # Environment variables (template, not committed)
├── .env.local                  # Local overrides (gitignored)
├── package.json                # Dependencies & scripts
├── pnpm-lock.yaml              # pnpm lockfile (DO NOT modify manually)
├── tsconfig.json               # TypeScript configuration
├── next.config.ts              # Next.js configuration
├── eslint.config.mjs           # ESLint configuration
├── postcss.config.mjs          # PostCSS & Tailwind setup
├── tailwind.config.ts          # Tailwind CSS configuration
├── commitlint.config.ts        # Commit message linting
├── components.json             # shadcn/ui configuration
└── .storybook/                 # Storybook configuration
    ├── main.ts
    └── preview.ts
```

**Environment Variables**:

**To customize this document:**

1. **Choose naming conventions** that match your stack
2. **Choose directory structure** (feature-based vs type-based)
3. **Define import strategy** (absolute vs relative, barrel files or not)
4. **Document special patterns** specific to your project
5. **Include examples** showing actual file paths from your project

## Summary: Key Rules for Osborn

**When creating new files, remember**:

1. ✅ **All filenames use kebab-case** (no exceptions)
2. ✅ **Components export with PascalCase names** (`export function LoginForm()`)
3. ✅ **Use `@/` for absolute imports** (avoid relative `../../../`)
4. ✅ **No barrel files** (except `/utils/index.ts`)
5. ✅ **Domain-specific code goes in `/domains/{domain}`**
6. ✅ **Global reusable components go in `/components`**
7. ✅ **Hooks use `use-` prefix** (`use-login-submit.ts`)
8. ✅ **Stores use `.store` suffix** (`auth.store.ts`)
9. ✅ **Schemas use `.schema` suffix** (`login.schema.ts`)
10. ✅ **Stories mirror component structure** (`/stories/components/atoms/button.stories.tsx`)

**Where to put files**:

| Type             | Location                                     | Example                                        |
| ---------------- | -------------------------------------------- | ---------------------------------------------- |
| Domain component | `/domains/{domain}/components/`              | `/domains/auth/components/card-login.tsx`      |
| Global component | `/components/{atoms\|molecules\|organisms}/` | `/components/atoms/button/button.tsx`          |
| Domain hook      | `/domains/{domain}/hooks/`                   | `/domains/auth/hooks/use-login-submit.ts`      |
| Domain store     | `/domains/{domain}/stores/`                  | `/domains/auth/stores/auth.store.ts`           |
| Domain schema    | `/domains/{domain}/`                         | `/domains/auth/login.schema.ts`                |
| Global utility   | `/utils/`                                    | `/utils/format-date.ts`                        |
| Global config    | `/config/`                                   | `/config/messages.ts`                          |
| Page             | `/app/{route}/`                              | `/app/dashboard/page.tsx`                      |
| Story            | `/stories/` (mirrors structure)              | `/stories/components/atoms/button.stories.tsx` |

---

**Token Budget**: ~500 tokens. Agents can read full or Grep specific sections.
