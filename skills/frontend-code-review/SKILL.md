---
name: frontend-code-review
description: Use when reviewing frontend code after implementation or between tasks - checks accessibility, responsive design, TypeScript strictness, component patterns, security, and UI state coverage. Includes mandatory completion gate.
---

# Frontend Code Review

Frontend-specific code review checklist. Complements general code review (superpowers:requesting-code-review if available). This skill focuses on frontend-specific quality that general reviewers miss.

## Context Loading

Before starting any review, read `.context/project-profile.md` and `.context/implementation-plan.md` (if they exist) to ground yourself in the project's conventions and the approved plan.

## When to Use

- After each task in frontend-task-orchestration
- Before marking any frontend work as complete
- When reviewing PRs that touch UI code

## The Frontend Review Checklist

### 1. TypeScript Strictness
- No `any` anywhere — check for `as any`, `: any`, `any[]`, `Record<string, any>`
- No `@ts-ignore` or `@ts-expect-error` without documented reason
- Props have explicit interfaces
- Event handlers use proper React/framework event types
- API responses are properly typed (not `any` or untyped `fetch`)

### 2. Accessibility
- Semantic HTML used (not div soup)
- Heading hierarchy correct (h1 > h2 > h3, no skipping)
- Interactive elements: `button` for actions, `a` for navigation
- Icon buttons have `aria-label`
- Forms have proper labels (not just placeholder)
- Color contrast meets WCAG AA
- Focus management on modals/route changes
- Keyboard navigable (tab through all interactive elements)

### 3. Responsive Design
- Works at 375px, 768px, 1280px minimum
- No horizontal overflow
- Touch targets >= 44px on mobile
- Text readable at all breakpoints
- Images don't break layout

### 4. Component Patterns
- Matches existing codebase conventions (reference `.context/project-profile.md`)
- Single responsibility — each component does one thing
- Props are minimal and well-typed
- State placed at correct level (no unnecessary lifting or prop drilling)
- No god components

### 5. UI States
- Loading state present for async operations
- Error state present with user-friendly message
- Empty state present (not just blank screen)
- Populated state renders correctly
- Edge cases: long text, missing optional data, rapid interactions

### 6. Security
- No unsafe innerHTML usage without sanitization (e.g., DOMPurify)
- No auth tokens in localStorage
- No API keys in client code
- User input sanitized before display
- URL params validated before use

### 7. Performance
- No unnecessary re-renders (check dependency arrays, memoization where needed)
- Images optimized (next/image or equivalent, proper sizing)
- Heavy components lazy loaded
- No blocking operations in render path

### 8. Bundle & Dependencies
- No full-library imports for single utilities (e.g. `import _ from 'lodash'` is wrong, use `import { debounce } from 'lodash/debounce'`)
- No wildcard imports (`import * as`) when named imports are available
- If new dependencies were added:
  - Check if the project already has a library for the same concern (reference `.context/project-profile.md`)
  - Verify imports are tree-shakeable (named imports, not default/wildcard)
  - Flag each new dependency to the user with justification and approximate size
  - If a lighter alternative exists, mention it

## Review Output Format

```
## Frontend Code Review

**Accessibility:** [PASS/ISSUES] - [details]
**TypeScript:** [PASS/ISSUES] - [details]
**Responsive:** [PASS/ISSUES] - [details]
**Components:** [PASS/ISSUES] - [details]
**UI States:** [PASS/ISSUES] - [details]
**Security:** [PASS/ISSUES] - [details]
**Performance:** [PASS/ISSUES] - [details]
**Bundle & Dependencies:** [PASS/ISSUES] - [details]

**Verdict:** [APPROVED / CHANGES REQUIRED]
```

Issues block approval. All 8 categories must pass.

---

## Completion Gate (MANDATORY)

**Before declaring ANY task complete, verify ALL of the following:**

1. **TypeScript compiles with zero errors** — run `tsc --noEmit`
2. **Linter passes with zero errors** — run the project's linter
3. **All existing tests still pass** — run the full test suite
4. **New tests exist for new functionality** — verify test files were created
5. **No `any` types introduced** — search for `any` in changed files
6. **Responsive at 3 breakpoints** — mobile 375px, tablet 768px, desktop 1280px
7. **Basic accessibility** — no missing alt text, all interactive elements focusable, semantic HTML
8. **No hardcoded secrets, API keys, or sensitive data in client code**
9. **Component matches the approved plan** — cross-reference `.context/implementation-plan.md`

**If any check fails, fix it before marking done.**

If a check cannot be run (e.g., no test runner configured, no dev server available), note it explicitly in the review output rather than silently skipping it.
