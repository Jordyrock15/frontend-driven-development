---
name: performance-bundle
description: Use after implementation to analyze bundle size, detect tree-shaking issues, flag missing lazy loading, and verify build output stays within budget
---

# Performance & Bundle Analysis

Run this after implementation is complete and before final sign-off. Catches bundle regressions, tree-shaking failures, and missing optimizations that code review alone won't find.

## When to Use

- After any Tier 2 or Tier 3 task that adds new dependencies or components
- When the user asks about bundle size or performance
- During the reunification phase of task orchestration
- When a new dependency is added mid-task

## Step 1: Production Build

Run the project's build command and capture output:

- **Next.js:** `npx next build` — outputs route sizes and first-load JS
- **Vite:** `npx vite build` — outputs chunk sizes
- **Webpack:** Check for `webpack-bundle-analyzer` or read build stats

If the build fails, stop — this is a build error, not a bundle issue. Use `frontend-driven-development:frontend-error-recovery`.

## Step 2: Analyze Output

From the build output, check:

### Chunk sizes
- Flag any chunk over **200kb** (gzipped) — these need investigation
- Flag any chunk over **500kb** as critical — must be addressed before shipping
- Identify which dependencies contribute to large chunks

### Route-level code splitting
- Every route/page should produce its own chunk
- Shared code should be in a commons chunk, not duplicated
- Flag routes that import heavy libraries that aren't needed at page load

### First-load JS (Next.js specific)
- Flag if first-load JS exceeds **100kb** for any route
- Check that the shared framework chunk isn't bloated by page-specific code

## Step 3: Dependency Audit

For each dependency added or modified in the current task:

1. **Check import style** — named imports only, no default/wildcard imports of large libraries
2. **Check for barrel file issues** — `import { Button } from '@/components'` can pull in the entire directory if barrel files re-export everything. Prefer direct file imports for large directories.
3. **Check for lighter alternatives:**
   - `date-fns` over `moment` (tree-shakeable)
   - `clsx` over `classnames` (smaller)
   - Native `fetch` over `axios` (zero-dependency)
   - `structuredClone()` over deep-clone libraries
4. **Check for duplicates** — two libraries solving the same problem (e.g., both `lodash` and `underscore`)

## Step 4: Lazy Loading Check

Verify lazy loading is applied where appropriate:

- **Route-level components** — should use `dynamic()` (Next.js), `lazy()` (React), or equivalent
- **Heavy third-party components** — modals, charts, editors, maps should be lazily loaded
- **Below-the-fold content** — components not visible on initial viewport can be deferred
- **Conditional renders** — components behind feature flags or user actions should be lazy

**Do NOT lazy-load:**
- Layout components (header, footer, sidebar)
- Above-the-fold content
- Components smaller than 5kb
- Shared utilities and hooks

## Step 5: Image Optimization

- Images use the framework's optimized component (`next/image`, etc.)
- Images have explicit `width` and `height` to prevent layout shift
- Offscreen images use lazy loading (`loading="lazy"` or framework equivalent)
- No uncompressed PNGs over 100kb — suggest WebP/AVIF

## Output

```
## Bundle Analysis

Build: [PASS/FAIL — did it build successfully?]
Total size: [first-load JS or total bundle size]

### Chunks
[List any chunks over 200kb with what's in them]

### Dependencies
[List any issues: barrel imports, missing tree-shaking, duplicates, heavy alternatives]

### Lazy Loading
[List components that should be lazy-loaded but aren't]

### Images
[List any unoptimized images]

### Verdict: [PASS / NEEDS ATTENTION]
```

If NEEDS ATTENTION, list specific fixes with file paths. Do not just flag — provide the fix.
