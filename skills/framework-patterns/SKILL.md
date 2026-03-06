---
name: framework-patterns
description: Use when making framework-specific implementation decisions - detects installed framework and provides patterns for React/Next.js, Vue, Svelte, or others based on what the project actually uses
---

## Detection First

Check what framework is installed via codebase-scan results. Do NOT assume React/Next.js if something else is present. Read `package.json` dependencies — use the project's actual framework.

## React / Next.js Patterns (default for greenfield)

- **App Router:** server components by default. Add `'use client'` only when needed (event handlers, hooks, browser APIs).
- **Data fetching:** server components for reads, Server Actions for mutations.
- **Client state:** local state first, then context, then external store — escalate only when needed.
- **Component files:** one component per file, co-locate styles and tests.
- **Tailwind:** utility-first. Extract components, not utility classes. Use `cn()` or `clsx()` for conditional classes.
- **CSS Modules:** use for component-scoped styles that Tailwind can't express cleanly.
- **Images:** `next/image` with explicit width/height, `priority` for above-fold.
- **Routing:** file-based. Use layouts for shared UI, `loading.tsx` and `error.tsx` for states.
- **Metadata:** `generateMetadata` for SEO on every page.
- **Error boundaries:** at route level minimum, more granular for complex features.

## Vue Patterns (when Vue detected)

- Composition API with `<script setup>`.
- Pinia for state management.
- Props validation with TypeScript interfaces.
- `defineEmits` with typed events.

## Svelte Patterns (when Svelte detected)

- Runes (`$state`, `$derived`, `$effect`) for reactivity.
- SvelteKit for routing and SSR.
- Form actions for mutations.

## General Patterns (all frameworks)

- Separate container/presentation when complexity warrants it.
- Co-locate related files (component + test + styles in same directory).
- Use the project's EXISTING conventions over "best practice" — consistency wins.
- Error boundaries/error states at route level minimum.
- Loading states are not optional — every async operation needs one.
- Forms: controlled inputs, proper validation, accessible error messages.

**The key rule: detect what's installed and adapt. Don't impose defaults.**
