---
name: frontend-workflow
description: Use when implementing any UI code - enforces the build, screenshot verify, responsive check, and accessibility audit loop before claiming work is complete
---

# Frontend Implementation Loop

Every UI change must pass all four phases. No exceptions. No skipping. No claiming completion without evidence from each phase.

```
Build -> Screenshot Verify -> Responsive Check -> Accessibility Audit
  ^            |                    |                    |
  |          FAIL?                FAIL?                FAIL?
  +------------+--------------------+--------------------+
                        FIX & RE-VERIFY
```

## Phase 1: Build

- Implement the component following approved architecture
- Use strict TypeScript types (reference `frontend-driven-development:typescript-strictness`)
- Follow framework patterns (reference `frontend-driven-development:framework-patterns`)
- Include ALL states: loading, error, empty, populated
- Handle edge cases: long text, missing data, rapid interactions

## Phase 2: Screenshot Verify (HARD GATE)

This is a hard gate. You may NOT proceed without passing it.

1. Start the dev server if not running
2. Navigate to the implemented UI
3. Take a screenshot or visually verify the output
4. Review: does it match the intended design/layout?
5. If issues found: fix and re-verify. Do NOT proceed until visual output is correct

**You may NOT claim UI work is done without visual evidence.**

## Phase 3: Responsive Check

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

## Phase 4: Accessibility Audit

**Semantic HTML:** Correct heading hierarchy (h1 > h2 > h3, no skipping). Use landmarks (`nav`, `main`, `footer`). Use lists for list content.

**Interactive elements:** `button` for actions, `a` for navigation. Never a `div` with `onClick`.

**ARIA:** Labels on icon buttons. Roles where semantic HTML is insufficient. Live regions for dynamic content.

**Keyboard:** All interactive elements focusable and operable via keyboard alone. Visible focus indicators required.

**Color contrast:** WCAG AA minimum — 4.5:1 for normal text, 3:1 for large text.

**Screen reader:** Meaningful alt text on images. Proper form labels. `aria-describedby` for help text.

**Focus management:** Trap focus in modals. Restore focus on close. Logical tab order.

## Completion

All four phases must pass. Report evidence from each phase:

1. **Build** — component renders without errors, all states implemented
2. **Screenshot** — visual output matches intent (attach screenshot or describe verification)
3. **Responsive** — confirmed at 375px, 768px, 1280px
4. **Accessibility** — semantic HTML, keyboard navigable, contrast passing, ARIA correct

If any phase fails, loop back and fix. Do not report completion until all four phases have passing evidence.
