---
description: Standards for React components, hooks, and state management
globs:
  - "src/components/**/*.tsx"
  - "src/layouts/**/*.tsx"
  - "src/hooks/**/*.ts"
  - "src/state/**/*.{ts,tsx}"
alwaysApply: false
---

## React Component & State Rule Set (LLM Mode: Architect, not Copycat)
- Production-grade Next.js/React 18 assumptions.  
- Prioritize clarity, testability, and SSR/ISR safety.

### Folder & Naming Conventions
- Components follow **PascalCase** filenames; co‑locate `index.ts` export barrels only for leaf folders.  
- One React component per file; co‑locate styles (CSS Modules / styled‑tsx) in same folder.  
- Root component folders: `ui/` (dumb presentational), `features/` (smart, uses hooks/services), `layouts/`.

### Hooks & State
- All reusable logic lives in `src/hooks/` or `src/state/`.  
- Use **custom hooks** for data fetching (`useUsers`, etc.); never call fetch or Firestore in components.  
- Prefer **Zustand** or **Redux Toolkit** for global state; avoid prop‑drilling > 2 levels.  
- Never mutate state directly; always use immutable updates or Immer.  
- SSR‑safety: Wrap Zustand store with createStore pattern to avoid cross‑request leaks.

```ts
// Good: createZustandStore.ts
import { create } from "zustand";

export const createUserStore = () =>
  create<{ user: User | null; setUser(u: User): void }>((set) => ({
    user: null,
    setUser: (user) => set({ user }),
  }));
```

### Component Patterns
- Use **function components** + hooks only—no legacy classes.  
- Extract expensive memoizable sub‑trees with `React.memo`.  
- Use `React.lazy` + suspense boundaries for non‑critical routes.  
- Never spread props blindly (`{...rest}`) unless typing with `ComponentProps<'button'>`.

### Accessibility & Styling
- Every interactive element needs an accessible name (`aria-label`, `<label>`).  
- Use semantic HTML first; ARIA only to patch gaps.  
- Style hierarchy: Tailwind classes → CSS Modules overrides → zero inline styles except dynamic tokens.

### SSR / ISR (Next.js)
- Data‑fetching hooks provide `options.ssr = true` to fetch on server when needed.  
- Avoid browser‑only APIs (`window`, `document`) without guards (`typeof window !== 'undefined'`).

### Testing
- All shared hooks/components require **Vitest + React Testing Library** specs in `__tests__/`.  
- Minimum coverage 80 % lines; CI fails below threshold.

## LLM Guidance
- Generate components inside `src/components/...` or `src/features/...` following folder rules.  
- Import state via dedicated hooks (`useUserStore`)—never global variables.  
- Avoid inline REST calls in JSX; wrap in services.  
- Ensure props and return types are fully typed with TypeScript.
