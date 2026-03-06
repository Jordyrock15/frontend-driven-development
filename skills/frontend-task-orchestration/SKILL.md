---
name: frontend-task-orchestration
description: Use after frontend architecture is approved and before implementation begins - breaks work into independent tasks, identifies parallelism, dispatches subagents, and reunifies with final verification
---

# Frontend Task Orchestration

**Triggers after:** `frontend-driven-development:frontend-architecture` has an approved plan from the user.

## Process (strict order)

### 1. Break Down the Approved Architecture into Tasks

Identify natural task boundaries in frontend work:

- **Shared types/interfaces** — must be FIRST, others depend on these
- **Shared/reusable components** — depend on types, other tasks depend on these
- **Feature-specific components** — often parallelizable with each other
- **Page composition/layout** — depends on feature components being complete
- **Tests per component** — can run in parallel per component

### 2. Build Dependency Graph

Determine which tasks can run in parallel (independent components, independent tests) and which must be sequential (types -> shared components -> feature components -> page). Produce a simple dependency list so ordering is explicit.

### 3. Create Task List

Use TodoWrite/TaskCreate to track all tasks. Each task gets: description, files to create/modify, dependencies, acceptance criteria, and which skills the subagent should follow.

### 4. Dispatch Subagents

One subagent per independent task. Each subagent receives:

- The approved architecture (relevant section)
- Codebase-scan results (conventions to follow)
- Its specific task with exact files and acceptance criteria
- Instructions to follow: `typescript-strictness`, `framework-patterns`, `frontend-workflow`

Dispatch in parallel where the dependency graph allows. Run sequentially where dependencies exist. Review between dependent stages for spec compliance and code quality.

**Task template for subagents:**

```
Task: [name]
Files: [create/modify list]
Dependencies: [what must exist first]
Props/Types: [from approved architecture]
Shared components to import: [list]
States to implement: loading, error, empty, populated
Testing: [what to test]
Skills to follow: typescript-strictness, framework-patterns, frontend-workflow
```

### 5. Reunify (HARD GATE — do not skip)

Individual components working does NOT mean the assembled UI works. You must:

- Verify all components compose correctly together
- Run the full test suite
- Final screenshot verification of the assembled UI
- Final accessibility audit of the complete page/feature

Do not mark the work as complete until reunification passes.

## Flowchart

```
Architecture approved
       |
  Break into tasks
       |
  Dependency graph
       |
  Dispatch parallel -----> Review
       |                     |
  More tasks? -- yes ------->|
       |
      no
       |
    Reunify
       |
     Done
```

## Key Principle

Break it down, parallelize what you can, reunify and verify at the end. The reunification gate exists because composition failures are invisible at the component level.
