---
name: frontend-workflow
description: Use when implementing any UI code - enforces the build, screenshot verify, responsive check, accessibility audit, and verification loops before claiming work is complete
---

# Frontend Implementation Loop

Every UI change must pass all phases. No exceptions. No skipping. No claiming completion without evidence from each phase.

## Context Loading

Before starting any work, read `.context/project-profile.md` and `.context/implementation-plan.md` (if they exist) to ground yourself in the project's conventions and the approved plan.

```
Build -> TypeScript Check -> Lint/Test -> Screenshot Verify -> Responsive Check -> Accessibility Audit
  ^            |                 |               |                    |                    |
  |          FAIL?             FAIL?           FAIL?                FAIL?                FAIL?
  +------------+-----------------+---------------+--------------------+--------------------+
                                    FIX & RE-VERIFY (max 3 iterations per phase)
```

## Phase 1: Build

- Implement the component following approved architecture (reference `.context/implementation-plan.md`)
- Use strict TypeScript types (reference `frontend-driven-development:typescript-strictness`)
- Follow framework patterns (reference `frontend-driven-development:framework-patterns`)
- Include ALL states: loading, error, empty, populated
- Handle edge cases: long text, missing data, rapid interactions

## Phase 2: TypeScript Compilation Loop

After writing code, verify types compile cleanly:

1. Run `tsc --noEmit` (or the project's equivalent type-check command)
2. If type errors exist → read the errors → fix the root cause → re-run
3. **Max 3 iterations.** If still failing after 3 attempts, stop and surface the remaining errors to the user with your diagnosis

Do NOT suppress errors with `any`, `@ts-ignore`, or `as unknown as Type`. Fix the actual types.

## Phase 3: Lint/Test Loop

Run the project's linter and test suite:

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

Verify at minimum three breakpoints:

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

## Completion

All phases must pass. Report evidence from each phase:

1. **Build** — component renders without errors, all states implemented
2. **TypeScript** — `tsc --noEmit` passes with zero errors
3. **Lint/Test** — linter passes, tests pass
4. **Screenshot** — visual output matches intent (attach screenshot or describe verification)
5. **Responsive** — confirmed at 375px, 768px, 1280px
6. **Accessibility** — semantic HTML, keyboard navigable, contrast passing, ARIA correct

If any phase fails, loop back and fix. Do not report completion until all phases have passing evidence.
