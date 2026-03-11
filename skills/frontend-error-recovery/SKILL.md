---
name: frontend-error-recovery
description: Use when encountering build failures, type errors, runtime errors, styling issues, test failures, or linter errors during frontend development - provides structured diagnosis and recovery instead of blind retries
---

# Frontend Error Recovery

**Purpose:** Structured diagnosis and recovery for common frontend failure categories. Follow this decision tree instead of blindly retrying.

## Context Loading

Before diagnosing, read `.context/project-profile.md` (if it exists) to understand the project's build tools, framework, and conventions — this often reveals the root cause faster.

## Core Principle

1. **Read the full error message** — not just the first line
2. **Classify the error** using the categories below
3. **Follow the decision tree** for that category
4. **Max 2 fix attempts per error.** If you can't resolve it after 2 tries, stop and surface the error to the user with your diagnosis and what you've tried. Do NOT silently retry in a loop.

---

## Error Categories

### 1. Type Errors (`tsc` / IDE red squiggles)

**Decision tree:**

```
Type error
  ├─ "Cannot find module" → Check import path, file exists, tsconfig paths/aliases
  ├─ "Property does not exist on type" → Check interface definition, missing optional `?`, wrong type narrowing
  ├─ "Type X is not assignable to type Y" → Compare the two types, find the mismatch, fix at source
  ├─ "Argument of type X is not assignable" → Check function signature, generic constraints
  ├─ "Object is possibly null/undefined" → Add null check or optional chaining, NOT non-null assertion
  ├─ "Generic type requires N type argument(s)" → Supply the generic parameter explicitly
  └─ Other → Read the full error, check the referenced line, trace the type back to its definition
```

**Never:** suppress with `any`, `@ts-ignore`, or `as unknown as Type`. Fix the actual type.

### 2. Build Failures (Vite, Next.js, Webpack)

**Decision tree:**

```
Build failure
  ├─ "Module not found" → Check: dependency installed? import path correct? case-sensitive filesystem?
  ├─ "Cannot resolve" → Check tsconfig paths, webpack aliases, missing file extension
  ├─ "Unexpected token" → Wrong file extension? Missing loader/plugin? JSX in .ts instead of .tsx?
  ├─ "Circular dependency" → Trace the import chain, extract shared types to a separate file
  ├─ "out of memory" / heap error → Check for infinite loops in config, or too many files being processed
  ├─ Config error → Read the config file, check for typos, validate against docs
  └─ Other → Read the full stack trace, identify which file/plugin is failing
```

**Check:** Is the error in your code or in a dependency? If dependency, check version compatibility.

### 3. Runtime Errors (browser console / server logs)

**Decision tree:**

```
Runtime error
  ├─ "Cannot read properties of null/undefined" → Trace where the value comes from, add loading/null state
  ├─ "X is not a function" → Wrong import? Method doesn't exist on that version? Typo?
  ├─ "Hydration mismatch" (Next.js/SSR) → Server and client render different output. Check:
  │     ├─ Date/time rendering (use suppressHydrationWarning or client-only)
  │     ├─ Browser-only APIs in server components (typeof window check)
  │     └─ Conditional rendering based on client state
  ├─ "Missing provider" / "useContext outside provider" → Wrap component tree with required provider
  ├─ "Too many re-renders" → Infinite loop in useEffect/setState. Check dependency arrays.
  ├─ "Unhandled promise rejection" → Missing error handling on async operation
  └─ Other → Read the stack trace, find the originating file and line
```

### 4. Styling Issues

**Decision tree:**

```
Styling issue
  ├─ Styles not applying →
  │     ├─ Tailwind: class exists? Check tailwind.config content paths. Run `npx tailwindcss` to verify.
  │     ├─ CSS Modules: using `.module.css` extension? Importing as `styles.className`?
  │     ├─ Styled-components: component rendered? Check SSR setup for hydration.
  │     └─ General: check specificity, inspect element in browser
  ├─ Wrong styles applying → Check specificity conflicts, class name collisions, import order
  ├─ Responsive breakpoint not working → Check media query syntax, Tailwind prefix order (sm: md: lg:)
  ├─ Layout broken → Check flex/grid properties, overflow settings, missing width/height constraints
  └─ Dark mode not working → Check Tailwind dark mode config (class vs media), theme provider
```

### 5. Test Failures

**Decision tree:**

```
Test failure
  ├─ "Cannot find module" in test → Check test config (jest.config/vitest.config), module name mapping, path aliases
  ├─ "act() warning" → Wrap state updates in act(), or use findBy* (async) instead of getBy*
  ├─ "Unable to find role/text" → Element not rendered? Check: async data loaded? Correct query? Case-sensitive?
  ├─ Snapshot mismatch → Is the change intentional? If yes, update snapshot. If no, fix the component.
  ├─ "not wrapped in act(...)" → Use waitFor() or findBy* for async updates
  ├─ Mock not working → Check mock path matches import path exactly, mock is hoisted correctly
  ├─ Environment error → Check test environment (jsdom vs node), missing polyfills, missing setup file
  └─ Timeout → Async operation not resolving? Check mock responses, increase timeout if needed
```

### 6. Linter Errors

**Decision tree:**

```
Linter error
  ├─ Auto-fixable → Run `eslint --fix` or `biome check --apply` first
  ├─ React hooks rules → Fix the hook call order/placement, don't just disable the rule
  ├─ Import order → Run auto-fix, or manually match project's import convention
  ├─ Unused variable → Remove it (don't prefix with _)
  ├─ Accessibility rule (jsx-a11y) → Fix the accessibility issue, don't disable the rule
  ├─ Conflicting rules → Check for ESLint + Prettier conflicts, use eslint-config-prettier
  └─ False positive → Only disable with inline comment + documented reason
```

---

## Recovery Protocol

When you hit an error during implementation:

1. **Stop.** Do not immediately retry the same thing.
2. **Read the full error** — including stack trace, file path, and line number.
3. **Classify** using the categories above.
4. **Follow the decision tree** for that category.
5. **Fix and verify** — run the relevant check again (`tsc --noEmit`, build, test, etc.).
6. **If the fix didn't work**, try ONE more approach from the decision tree.
7. **After 2 failed attempts**, surface to the user:
   - The full error message
   - What category you classified it as
   - What you tried and why it didn't work
   - Your best guess at the root cause

**Never:** silently suppress errors, add `any` to fix type errors, disable linter rules without reason, or retry the same failing approach more than twice.
