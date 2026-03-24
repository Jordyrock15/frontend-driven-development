---
name: frontend-task-orchestration
description: Use after frontend architecture is approved and before implementation begins - breaks work into independent tasks, identifies parallelism, dispatches subagents, and reunifies with final verification
---

# Frontend Task Orchestration

**Triggers after:** `frontend-driven-development:frontend-architecture` has an approved plan from the user.

## Process (strict order)

### 1. Break Down the Approved Architecture into Tasks

Reference the component breakdown and acceptance criteria in `.context/implementation-plan.md`. Identify natural task boundaries:

- **Shared types/interfaces** — must be FIRST, others depend on these
- **Shared/reusable components** — depend on types, other tasks depend on these
- **Feature-specific components** — often parallelizable with each other
- **Page composition/layout** — depends on feature components being complete
- **Tests per component** — can run in parallel per component

### 2. Build Dependency Graph

Determine which tasks can run in parallel (independent components, independent tests) and which must be sequential (types → shared components → feature components → page). Produce a simple dependency list so ordering is explicit.

### 3. Create Task List

Use TodoWrite/TaskCreate to track all tasks. Each task gets: description, files to create/modify, dependencies, acceptance criteria, and which skills the subagent should follow.

### 4. Dispatch Subagents

One subagent per independent task. Each subagent receives a complete, self-contained prompt — do not assume subagents have access to the parent conversation or skill definitions.

**Subagent prompt template:**

```
## Context (read these files first)
- .context/project-profile.md — project conventions, framework, styling, patterns
- .context/implementation-plan.md — approved architecture (focus on the section relevant to your task)

## Your Task
Name: [task name]
Files to create/modify: [exact file paths]
Dependencies: [what must exist before you start — verify these files exist]

## Requirements
- Props/Types: [paste the exact interfaces from the approved plan]
- Shared components to import: [list with file paths]
- States to implement: loading, error, empty, populated
- Acceptance criteria: [paste from implementation plan]

## Rules
- Strict TypeScript — never use `any`, `as any`, or `@ts-ignore`
- Match the project's existing patterns (naming, styling, file structure) as documented in project-profile.md
- Named imports only — no wildcard or full-library imports
- Run `tsc --noEmit` after implementation to verify types compile

## Testing
- [what to test, which test patterns to follow from project-profile.md]
- Follow `frontend-driven-development:frontend-testing` patterns: test behaviour not implementation, use Testing Library query priority (getByRole > getByLabelText > getByText > getByTestId), include axe-core accessibility checks

## When to stop
- After 2 failed attempts to fix the same error, report the error and your diagnosis instead of continuing
- If requirements are ambiguous, report what's unclear instead of guessing
```

**Dispatch rules:**
- Launch all independent tasks in parallel using the Agent tool
- Run sequential tasks only after their dependencies are confirmed complete
- Review output between dependent stages for spec compliance before continuing

### 5. Reunify (HARD GATE — do not skip)

Individual components working does NOT mean the assembled UI works. You must:

1. Verify all components compose correctly together
2. Run `frontend-driven-development:frontend-workflow` for the composed result (covers TypeScript, lint, tests, screenshot, responsive, accessibility, code review)
3. Run `frontend-driven-development:performance-bundle` if new dependencies or components were added
4. Run `frontend-driven-development:git-workflow` for commit hygiene and PR readiness

Do not mark the work as complete until reunification passes.

## Key Principle

Break it down, parallelize what you can, reunify and verify at the end. The reunification gate exists because composition failures are invisible at the component level.
