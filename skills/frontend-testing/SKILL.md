---
name: frontend-testing
description: Use when writing or modifying frontend tests - adapts to installed testing tools, covers E2E, unit, integration, and accessibility testing with proper query patterns
---

## Detection First

Check codebase-scan results for installed testing tools before giving any guidance. Adapt ALL recommendations to the actual tools in the project. Do NOT assume Playwright/Vitest if the project uses Cypress, Jest, or something else. Match the project's existing patterns.

**Default tools (greenfield projects only):** Playwright for E2E, Vitest + React Testing Library for unit/integration.

## What to Test (Priority Order)

1. **User interactions** — click, type, submit, navigate
2. **Conditional rendering** — loading, error, empty, populated states
3. **Accessibility** — axe-core integration, keyboard navigation
4. **Data flow** — props render correctly, state updates reflect in UI
5. **Edge cases** — long text, missing data, rapid interactions, empty arrays

## Testing Principles

- **Test behavior, not implementation** — query by role/label, not class/testid
- **Testing Library query priority:** `getByRole` > `getByLabelText` > `getByText` > `getByPlaceholderText` > `getByTestId` (last resort)
- **E2E tests** for critical user journeys (login, checkout, core flows)
- **Unit tests** for complex logic, data transformations, custom hooks
- **Integration tests** for component composition and data flow
- Each test should break if the feature breaks, pass if it works

## Accessibility Testing

- Include `axe-core` checks in integration tests (e.g., `@axe-core/playwright` or `jest-axe`)
- Test keyboard navigation flows in E2E
- Verify focus management on route changes and modal open/close

## Anti-Patterns to Reject

| Anti-pattern | Why it's wrong |
|---|---|
| Snapshot tests as primary strategy | Approved without reading, breaks on irrelevant changes |
| Testing implementation details | Breaks on refactor, doesn't catch user-facing bugs |
| `getByTestId` for everything | Ignores accessibility, misses missing labels |
| Mocking everything | Tests mock behavior, not real behavior |
| Testing library internals | Testing that React re-renders is not your job |
| Tests that pass when feature is broken | Test is useless — delete it |
