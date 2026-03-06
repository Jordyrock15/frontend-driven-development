---
name: frontend-code-review
description: Use when reviewing frontend code after implementation or between tasks - checks accessibility, responsive design, TypeScript strictness, component patterns, security, and UI state coverage
---

# Frontend Code Review

Frontend-specific code review checklist. Complements general code review (superpowers:requesting-code-review if available). This skill focuses on frontend-specific quality that general reviewers miss.

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
- Matches existing codebase conventions (reference codebase-scan findings)
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

**Verdict:** [APPROVED / CHANGES REQUIRED]
```

Issues block approval. All 7 categories must pass.
