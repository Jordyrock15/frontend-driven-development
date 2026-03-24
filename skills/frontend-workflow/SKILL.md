---
name: frontend-workflow
description: Use when implementing any UI code - enforces the build, screenshot verify, responsive check, accessibility audit, code review checklist, and verification loops before claiming work is complete
---

# Frontend Implementation Loop

Every UI change must pass all phases. No exceptions. No skipping. No claiming completion without evidence from each phase.

```
Build -> TypeScript Check -> Lint/Test -> Screenshot Verify -> Responsive Check -> Accessibility Audit -> Code Review
  ^            |                 |               |                    |                    |                    |
  |          FAIL?             FAIL?           FAIL?                FAIL?                FAIL?               FAIL?
  +------------+-----------------+---------------+--------------------+--------------------+--------------------+
                                    FIX & RE-VERIFY (max 3 iterations per phase)
```

## Phase 1: Build

- Implement the component following approved architecture (reference `.context/implementation-plan.md`)
- Use strict TypeScript types (reference `frontend-driven-development:typescript-strictness`)
- Follow framework patterns (reference `frontend-driven-development:framework-patterns`)
- Include ALL states: loading, error, empty, populated
- Handle edge cases: long text, missing data, rapid interactions

## Phase 2+3: TypeScript + Lint/Test (run in parallel)

**Run TypeScript compilation and lint/test concurrently** — they are independent checks. Fix errors from whichever fails.

### TypeScript Compilation

1. Run `tsc --noEmit` (or the project's equivalent type-check command)
2. If type errors exist → read the errors → fix the root cause → re-run
3. **Max 3 iterations.** If still failing after 3 attempts, stop and surface the remaining errors to the user with your diagnosis

Do NOT suppress errors with `any`, `@ts-ignore`, or `as unknown as Type`. Fix the actual types.

### Lint/Test

1. **Lint:** Run the project's linter (ESLint, Biome, etc. — detected from `.context/project-profile.md`)
   - If failures → apply auto-fixes where available → re-run
   - Manual fixes for non-auto-fixable issues
   - **Max 2 iterations**
2. **Test:** Run tests for affected files (using the project's test runner)
   - If test failures → read the failure output → fix → re-run
   - **Max 2 iterations**
3. If still failing after iterations, stop and surface errors to the user

## Phase 4: Screenshot Verify Loop (HARD GATE)

This is a hard gate. You may NOT proceed without passing it.

1. Start the dev server if not running
2. Navigate to the implemented UI
3. Take a screenshot or visually verify the output
4. **Evaluate:** Does it match the design intent and the approved plan in `.context/implementation-plan.md`?
5. If issues found → fix → re-screenshot
6. **Max 3 iterations.** If still not matching after 3 attempts, flag for human review with a description of what's wrong and what you've tried

**You may NOT claim UI work is done without visual evidence.**

## Phase 5: Responsive Check

Verify at minimum three breakpoints. **Screenshot all 3 breakpoints in parallel** — they are independent checks.

| Breakpoint | Width |
|------------|-------|
| Mobile     | 375px |
| Tablet     | 768px |
| Desktop    | 1280px |

Check at each breakpoint:

- Layout integrity — no broken layouts or overlapping elements
- Text readability — no truncation that hides meaning
- Touch targets — minimum 44px on mobile
- No horizontal overflow

Mobile-first: the mobile experience is not an afterthought.

## Phase 6: Accessibility Audit

**Semantic HTML:** Correct heading hierarchy (h1 > h2 > h3, no skipping). Use landmarks (`nav`, `main`, `footer`). Use lists for list content.

**Interactive elements:** `button` for actions, `a` for navigation. Never a `div` with `onClick`.

**ARIA:** Labels on icon buttons. Roles where semantic HTML is insufficient. Live regions for dynamic content.

**Keyboard:** All interactive elements focusable and operable via keyboard alone. Visible focus indicators required.

**Color contrast:** WCAG AA minimum — 4.5:1 for normal text, 3:1 for large text.

**Screen reader:** Meaningful alt text on images. Proper form labels. `aria-describedby` for help text.

**Focus management:** Trap focus in modals. Restore focus on close. Logical tab order.

## Phase 7: Code Review Checklist

Run through this checklist against all changed files. Every category must PASS.

### TypeScript Strictness
- No `any` anywhere — check for `as any`, `: any`, `any[]`, `Record<string, any>`
- No `@ts-ignore` or `@ts-expect-error` without documented reason
- Props have explicit interfaces
- Event handlers use proper React/framework event types
- API responses are properly typed

### Component Patterns
- Matches existing codebase conventions (reference `.context/project-profile.md`)
- Single responsibility — each component does one thing
- Props are minimal and well-typed
- State placed at correct level (no unnecessary lifting or prop drilling)

### UI States
- Loading state present for async operations
- Error state present with user-friendly message
- Empty state present (not just blank screen)
- Edge cases: long text, missing optional data, rapid interactions

### Security
- No unsafe innerHTML usage without sanitization
- No auth tokens in localStorage
- No API keys in client code
- User input sanitized before display
- URL params validated before use

### Performance
- No unnecessary re-renders (check dependency arrays, memoization where needed)
- Images optimized (next/image or equivalent, proper sizing)
- Heavy components lazy loaded
- No blocking operations in render path

### Bundle & Dependencies
- No full-library imports (`import _ from 'lodash'` → `import { debounce } from 'lodash/debounce'`)
- No wildcard imports when named imports are available
- New dependencies flagged to user with justification and approximate size
- Check if project already has a library for the same concern

**Review output format:**

```
## Code Review
TypeScript:    [PASS/FAIL]
Components:    [PASS/FAIL]
UI States:     [PASS/FAIL]
Security:      [PASS/FAIL]
Performance:   [PASS/FAIL]
Bundle:        [PASS/FAIL]
Verdict:       [APPROVED / CHANGES REQUIRED]
```

Any FAIL blocks approval. Fix before proceeding.

## Completion Gate (MANDATORY)

All phases above must PASS. Do not declare the task complete until every phase has passing evidence. If a phase cannot be run (e.g., no test runner configured, no dev server available), note it explicitly rather than silently skipping it.

**Additional checks not covered by earlier phases:**
- New tests exist for new functionality (not just that existing tests pass)
- No `any` types introduced — grep changed files
- No hardcoded secrets or API keys in client code
- Component matches the approved plan — cross-reference `.context/implementation-plan.md`

**If any check fails, fix it before marking done.**
