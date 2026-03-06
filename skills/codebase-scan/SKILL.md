---
name: codebase-scan
description: Use when starting any frontend work - detects project framework, styling, testing tools, component patterns, and conventions before making architecture or implementation decisions
---

# Codebase Scan

**Purpose:** Single source of truth for project conventions. Run this before making any architecture or implementation decisions.

## Scan Process

Execute these steps in order:

### Step 1: Read `package.json`

Extract `dependencies` and `devDependencies`. From these, detect:

- **Framework:** react, next, vue, nuxt, svelte, angular, astro, solid-js, etc.
- **Styling:** tailwindcss, styled-components, @emotion/*, sass, less. Also check for CSS module usage in step 4.
- **Testing:** playwright, vitest, jest, cypress, @testing-library/*.
- **State management:** zustand, redux, @reduxjs/toolkit, jotai, recoil, @tanstack/react-query, valtio.
- **Shared components / UI libraries:** @radix-ui/*, @shadcn/ui, @mui/material, antd, @chakra-ui/react.

### Step 2: Glob for config files

Search for: `*.config.*`, `tsconfig.json`, `.eslintrc*`, `.prettierrc*`, `biome.json`, `postcss.config.*`, `tailwind.config.*`.

Detect from these:

- **Linting/formatting:** ESLint (eslint.config or .eslintrc*), Prettier (.prettierrc* or prettier.config), Biome (biome.json).
- **Build tools:** Vite (vite.config), Next.js (next.config), Webpack (webpack.config), tsconfig paths/aliases.
- **Styling (confirmation):** tailwind.config, postcss.config.

### Step 3: Glob for component directories

Search for: `src/components`, `src/app`, `src/pages`, `app/`, `pages/`, `src/features`, `src/ui`.

Determine folder structure: feature-based, atomic, flat, or other.

Check for barrel exports (`index.ts` re-exports) in component directories.

Check for shared/common/ui component directories.

### Step 4: Read 3-5 representative components

Pick components from different directories. For each, detect:

- **Naming convention:** PascalCase filenames? kebab-case?
- **Props patterns:** TypeScript interfaces vs type aliases?
- **Styling approach:** CSS modules (.module.css imports), Tailwind classes, styled-components, inline styles?
- **State management patterns:** React context usage, hooks, store imports?

### Step 5: Check test files

Glob for `**/*.test.*`, `**/*.spec.*`, `**/__tests__/**`.

Detect: co-located tests (next to components) vs separate `__tests__/` directories. Note testing patterns (render, screen, expect conventions).

## Output

Summarize findings before proceeding. List what was detected for each category:

1. Framework and version
2. Styling approach
3. Testing tools and conventions
4. State management
5. Component patterns (naming, structure, props)
6. Shared component library
7. Linting/formatting
8. Build tools and aliases

Flag anything unusual or conflicting (e.g., both Jest and Vitest present, mixed naming conventions).

## Greenfield Defaults

When nothing is detected (new project), default to:

- React 19 + Next.js (App Router)
- Tailwind CSS
- TypeScript strict mode
- Playwright (E2E) + Vitest (unit)
- Feature-based folder structure
