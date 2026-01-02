# Tech Stack

**Technologies, versions, commands, and library usage.**

**Purpose**: Quick reference for AI agents to know what technologies are used, their versions, and common commands.

---

## How to Use This Document

Document your project's tech stack including:

1. **Core Technologies**: Framework, language, build tools
2. **Key Dependencies**: UI libraries, state management, testing, etc.
3. **Development Tools**: Package manager, linter, formatter
4. **Commands**: Common development commands
5. **Important Notes**: Version constraints, tool preferences

**Token Size**: ~300-500 tokens

**Agents**: Read this when they need to know library versions, commands, or tool usage

---

## Template Structure

Below is a **template with placeholders**. Replace all examples with your actual tech stack.

---

## Core Stack

- **Framework**: Next.js 15.3.8 (App Router with Turbopack)
- **Language**: TypeScript 5.x (strict mode enabled)
- **Build Tool**: Next.js (with Turbopack for dev, Webpack for prod)
- **Package Manager**: **pnpm 8.6.3**
- **Runtime Version**: Node.js >= 20.11.0 (LTS)

⚠️ **CRITICAL**: **ONLY use pnpm** - Never use npm, yarn, or bun. The project specifies `pnpm@8.6.3` as the package manager.

---

## Key Dependencies

### UI & Styling

- **Component Library**: shadcn/ui - Radix UI primitives (latest)
- **Styling**: Tailwind CSS - 4.1.12
- **Icons**: lucide-react - 0.503.0
- **Class Utilities**:
  - clsx - 2.1.1 (conditional classes)
  - tailwind-merge - 3.2.0 (merge Tailwind classes)
  - class-variance-authority - 0.7.1 (component variants)
- **Animations**: tw-animate-css - 1.2.8

**Radix UI Components**:

- @radix-ui/react-checkbox - 1.3.2
- @radix-ui/react-dialog - 1.1.15
- @radix-ui/react-label - 2.1.7
- @radix-ui/react-popover - 1.1.15
- @radix-ui/react-radio-group - 1.3.8
- @radix-ui/react-select - 2.2.5
- @radix-ui/react-slot - 1.2.3
- @radix-ui/react-tooltip - 1.2.7

**Usage Notes**:

- Use Tailwind utilities for all styling (minimal custom CSS)
- Use `@apply` directive in separate CSS files for repeated styles
- Use shadcn/ui components from `/components/ui` (copied via CLI)
- Use `cn()` utility from `/lib/utils` for conditional classes

---

### State Management

- **Client State**: Zustand - 5.0.6
- **Form State**: React Hook Form - 7.56.4
- **URL State**: nuqs (for URL-based state, if used)

**Decision Matrix**:
| State Type | Library | Use For |
|------------|---------|---------|
| Client state | Zustand | UI state, auth state, preferences |
| Form state | React Hook Form | Form handling & validation |
| Local state | useState | Component-only state |
| URL state | nuqs | Shareable state in URL params |

**Usage Notes**:

- Prefer server components over client state when possible
- Use Zustand stores in `/domains/{domain}/stores/` for domain state
- Use React Hook Form with Zod for all forms
- Minimize client-side state (leverage Next.js server capabilities)

---

### Forms & Validation

- **Form Library**: React Hook Form - 7.56.4
- **Validation**: Zod - 3.25.30
- **Form Resolver**: @hookform/resolvers - 5.0.1

**Usage Notes**:

- Always use Zod schemas for form validation
- Define schemas in `{name}.schema.ts` files
- Infer types from schemas: `type FormData = z.infer<typeof schema>`
- Use `useForm` with `zodResolver` for integration

---

### Authentication

- **Auth Library**: NextAuth.js - 5.0.0-beta.30
- **Strategy**: Credentials + OKTA provider

**Usage Notes**:

- Auth configuration in `/lib/auth.ts`
- API routes in `/app/api/auth/[...nextauth]/route.ts`
- Use `useAuthStore` from `/domains/auth/stores/auth.store.ts` for client state
- Server components can access session via NextAuth utilities

---

### Development & Documentation

- **Storybook**: 8.6.14
  - @storybook/nextjs
  - @storybook/react
  - @storybook/addon-essentials
  - @storybook/addon-styling-webpack
  - @storybook/experimental-nextjs-vite

**Usage Notes**:

- Stories in `/stories` mirror component structure
- Use `tags: ['autodocs']` for automatic documentation
- Organize stories by domain: `/stories/components` or `/stories/domains`

---

## Development Tools

### Package Manager

**Package Manager**: **pnpm 8.6.3**

⚠️ **CRITICAL**: **ONLY use pnpm** - Never use npm, yarn, or bun

```bash
# ✅ CORRECT
pnpm install
pnpm add package-name
pnpm add -D package-name
pnpm remove package-name

# ❌ WRONG - DO NOT USE
npm install
yarn add package-name
bun install
```

**Why pnpm**:

- Specified in `package.json` as `"packageManager": "pnpm@8.6.3"`
- Efficient disk space usage with content-addressable storage
- Strict dependency resolution
- Fast installation with parallel operations

---

### Code Quality

- **Linter**: ESLint 9.x
- **Formatter**: Prettier 3.5.3
- **Git Hooks**: Husky 9.1.7 + lint-staged 15.5.2
- **Commit Linting**: commitlint with conventional config 19.8.1

**ESLint Plugins**:

- @typescript-eslint/eslint-plugin - 8.32.0
- @typescript-eslint/parser - 8.32.0
- eslint-plugin-prettier - 5.4.0
- eslint-plugin-storybook - 0.12.0
- eslint-config-next - 15.3.1

**Prettier Plugins**:

- prettier-plugin-tailwindcss - 0.6.11 (auto-sort Tailwind classes)

**Formatting Rules**:

- **Indentation**: 2 spaces
- **Quotes**: Single quotes for JS/TS, double quotes for JSX
- **Semicolons**: Yes (automatic)
- **Line width**: 80 characters
- **Trailing commas**: ES5
- **Arrow function parens**: Always

**Git Hooks**:

```json
// lint-staged config in package.json
{
  "src/**/*": ["pnpm prettier:format", "pnpm eslint:format"]
}
```

---

### TypeScript Configuration

**Important Settings**:

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2017",
    "lib": ["dom", "dom.iterable", "esnext"],
    "jsx": "preserve",
    "module": "esnext",
    "moduleResolution": "bundler",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

⚠️ **Critical Rules**:

- **No `any` types** - `@typescript-eslint/no-explicit-any: error`
- **Strict mode enabled** - catches potential bugs
- **No unused variables** - `@typescript-eslint/no-unused-vars: error`
- **camelCase naming** - enforced except for `api_url` and `Geist_Mono`
- **No console** - `no-console: warn` (use proper logging)

---

### Testing

- **Test Framework**: Vitest - 3.1.3
- **Coverage**: @vitest/coverage-v8 - 3.1.3
- **Browser Testing**: @vitest/browser - 3.1.3

**Usage Notes**:

- Test files colocated with source: `{filename}.test.ts`
- Run tests with `pnpm test` (not currently in package.json, may need to add)

---

## Commands

### Installation

```bash
# Install dependencies
pnpm install

# Add dependency
pnpm add package-name

# Add dev dependency
pnpm add -D package-name

# Remove dependency
pnpm remove package-name
```

⚠️ **NEVER use npm, yarn, or bun for package management**

---

### Development

```bash
# Start dev server (with Turbopack)
pnpm dev

# Build for production
pnpm build

# Start production server
pnpm start

# Preview production build (build + start)
pnpm preview
```

**Dev Server**: Runs on `http://localhost:3000` by default

---

### Code Quality

```bash
# Lint code
pnpm lint

# Format code with Prettier
pnpm prettier:format

# Fix ESLint errors
pnpm eslint:format

# Run both formatters (done automatically in pre-commit hook)
pnpm prettier:format && pnpm eslint:format
```

**Note**: Formatting runs automatically on commit via Husky + lint-staged

---

### Storybook

```bash
# Start Storybook dev server (port 6006)
pnpm storybook

# Build Storybook for production
pnpm build-storybook
```

**Storybook Server**: Runs on `http://localhost:6006`

---

### Git Hooks

```bash
# Setup Husky hooks (runs automatically after install)
pnpm prepare
```

**Pre-commit Hook**: Automatically runs Prettier and ESLint on staged files

**Commit Message Linting**: Enforces conventional commit format via commitlint

---

### Testing (Future)

Testing is configured but scripts may need to be added:

```bash
# Run tests (add to package.json if needed)
pnpm test

# Run tests in watch mode
pnpm test:watch

# Run tests with coverage
pnpm test:coverage
```

---

## Library-Specific Patterns

### Zustand Stores

**Setup**:

```typescript
// domains/{domain}/stores/{name}.store.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface State {
  // State properties
}

interface Actions {
  // Action methods
}

export const useMyStore = create<State & Actions>()(
  persist(
    (set, get) => ({
      // Initial state
      // Actions
    }),
    { name: 'store-name' } // localStorage key
  )
);
```

**Usage**:

```typescript
'use client';  // Required for client components

import { useMyStore } from '@/domains/{domain}/stores/my.store';

export function MyComponent() {
  const { state, action } = useMyStore();

  return <div onClick={action}>{state}</div>;
}
```

---

### React Hook Form + Zod

**Setup**:

```typescript
// 1. Define schema
import { z } from 'zod';

export const loginSchema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Min 8 characters')
});

export type LoginFormData = z.infer<typeof loginSchema>;

// 2. Use in component
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

export function LoginForm() {
  const form = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
    defaultValues: {
      email: '',
      password: ''
    }
  });

  const onSubmit = (data: LoginFormData) => {
    // Handle submission
  };

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {/* Form fields */}
    </form>
  );
}
```

---

### Tailwind CSS with CVA

**Setup**:

```typescript
// Using class-variance-authority for component variants
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  'base-classes-here',
  {
    variants: {
      variant: {
        default: 'bg-primary text-white',
        outline: 'border border-primary',
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4',
        lg: 'h-12 px-6 text-lg',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'md',
    },
  }
);

interface ButtonProps extends VariantProps<typeof buttonVariants> {
  className?: string;
}

export function Button({ variant, size, className }: ButtonProps) {
  return (
    <button className={cn(buttonVariants({ variant, size }), className)}>
      Button
    </button>
  );
}
```

---

### NextAuth.js

**Configuration** (`/lib/auth.ts`):

```typescript
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [
    Credentials({
      credentials: {
        email: {},
        password: {}
      },
      authorize: async credentials => {
        // Validate credentials
        return user;
      }
    })
  ]
});
```

**Usage**:

```typescript
// Client component
'use client';
import { signIn, signOut } from 'next-auth/react';

// Server component
import { auth } from '@/lib/auth';

export async function ServerComponent() {
  const session = await auth();
  // Use session
}
```

---

## Important Notes

### Version Constraints

- **Node.js**: >= 20.11.0 LTS (specified in `engines` field)
- **pnpm**: 8.6.3 (specified in `packageManager` field)
- **React**: 19.0.0 (latest with concurrent features)
- **Next.js**: 15.3.8 (App Router with React Server Components)
- **TypeScript**: 5.x (for latest type system features)
- **Tailwind CSS**: 4.x (latest major version with improved performance)

**Why these versions**:

- Node 20+ required for Next.js 15 compatibility
- React 19 for latest RSC features
- TypeScript 5 for better type inference and performance
- pnpm 8.6.3 locked for consistency

---

### Tool Preferences

⚠️ **Package Manager**: **ALWAYS use pnpm** (never npm, yarn, bun)

⚠️ **Component Library**: **shadcn/ui + Radix UI** (NOT Material-UI, Chakra, Ant Design)

⚠️ **Styling**: **Tailwind CSS with `@apply` for repeated patterns** (minimal custom CSS)

⚠️ **State Management**:

- **Zustand for client state** (NOT Redux, MobX, Recoil)
- **React Hook Form for forms** (NOT Formik)
- **Zod for validation** (NOT Yup, Joi)

⚠️ **Testing**: **Vitest** (NOT Jest directly, though similar API)

⚠️ **Icons**: **lucide-react** (consistent icon library)

---

### Environment Variables

**File**: `.env.local` (for local development, gitignored)

**Required Variables**:

```bash
# Next.js App URL
NEXT_PUBLIC_APP_URL=http://localhost:3000

# NextAuth Configuration
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-secret-key-here

# OKTA Configuration (if using OKTA provider)
OKTA_CLIENT_ID=your-okta-client-id
OKTA_CLIENT_SECRET=your-okta-client-secret
OKTA_ISSUER=https://your-domain.okta.com
```

**Access in Code**:

```typescript
// Next.js environment variables
// Public variables (accessible in browser)
const appUrl = process.env.NEXT_PUBLIC_APP_URL;

// Server-only variables (NOT accessible in browser)
const secret = process.env.NEXTAUTH_SECRET;
```

⚠️ **CRITICAL**:

- Prefix with `NEXT_PUBLIC_` for client-side access
- Never commit `.env.local` (it's gitignored)
- Use `.env` for default/template values

---

## Troubleshooting

### Common Issues

**Issue**: Package installation fails with permission errors

**Solution**:

```bash
# Clear pnpm cache
pnpm store prune

# Remove node_modules and lockfile
rm -rf node_modules pnpm-lock.yaml

# Reinstall
pnpm install
```

---

**Issue**: "Cannot find module '@/...' " import errors

**Solution**:

- Check `tsconfig.json` has `"@/*": ["./src/*"]` in paths
- Restart TypeScript server in IDE
- Ensure imports use `@/` prefix for absolute paths

---

**Issue**: Tailwind classes not applying

**Solution**:

```bash
# Restart dev server
# Tailwind with Turbopack may need restart after config changes
pnpm dev
```

---

**Issue**: ESLint errors about unused variables or camelCase

**Solution**:

- Remove unused imports/variables (rule is set to `error`)
- Use camelCase for all variables (except `api_url`, `Geist_Mono`)
- Prefix unused parameters with `_` if intentionally unused

---

**Issue**: Commit blocked by lint-staged

**Solution**:

```bash
# Fix formatting issues
pnpm prettier:format
pnpm eslint:format

# Stage fixes and commit again
git add .
git commit -m "your message"
```

---

## Summary: Key Technical Decisions

1. ✅ **Next.js 15 App Router** - Server-first architecture with RSC
2. ✅ **pnpm only** - Never npm/yarn/bun
3. ✅ **TypeScript strict mode** - No `any`, unused vars are errors
4. ✅ **Tailwind CSS 4** - Utility-first with `@apply` for patterns
5. ✅ **Zustand for client state** - Simple, powerful, no provider needed
6. ✅ **React Hook Form + Zod** - Type-safe form handling
7. ✅ **shadcn/ui + Radix UI** - Headless, accessible components
8. ✅ **Storybook** - Component documentation and testing
9. ✅ **NextAuth** - Authentication with credentials + OKTA
10. ✅ **Screaming Architecture** - Domain-driven file structure

---

**Token Budget**: ~300-500 tokens. Agents can read full document or Grep for specific sections (e.g., commands, library usage).
