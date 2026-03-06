---
name: frontend-architecture
description: Use when building new components, pages, or features - enforces planning with clarifying questions, component breakdown, state design, and user approval before any code is written
---

## HARD GATE

Do NOT write any component code until the architecture is approved by the user. No exceptions. This skill enforces a gate, not a suggestion.

## Process (strict order)

```
Scan -> Questions -> Propose -> Approve -> Orchestrate
```

### 1. Run codebase-scan first

Before proposing anything, run `frontend-driven-development:codebase-scan` to understand existing patterns, shared components, state management approaches, and conventions already in use.

### 2. Ask clarifying questions ONE AT A TIME

Ask only one question per message. Prefer multiple choice when possible.

- What is this feature/page/component for? Who uses it?
- What data does it need? Where does it come from (API, props, URL params, local state)?
- What user interactions are involved (clicks, forms, drag, scroll)?
- What states exist? (loading, error, empty, populated, disabled, etc.)
- Does this need shared state or is local state sufficient?
- Which existing shared components can be reused?
- Any specific responsive or accessibility requirements beyond defaults?

Do not batch questions. Wait for each answer before asking the next.

### 3. Propose component breakdown (section by section, get approval after each)

- **Component tree** — hierarchy with parent/child relationships
- **Props** — TypeScript interfaces for each component
- **State placement** — which component owns what state and why
- **Data flow** — how data moves through the tree
- **Shared vs feature-specific** — identify new shared components vs one-off components

Present one section at a time. Get approval before moving to the next.

### 4. Consistency check

Compare the proposed architecture against codebase-scan findings. Flag any deviations from existing patterns and explicitly justify each one.

### 5. User approves

Do not proceed until the user gives explicit approval of the full architecture.

## Anti-patterns to catch during planning

- **Prop drilling more than 2 levels** — suggest composition or context instead
- **God components** (more than one responsibility) — break them down
- **Mixing data fetching and presentation** — separate concerns
- **Reinventing patterns** — do not create new patterns when existing project patterns work fine
- **Over-abstracting** — do not create abstractions for single-use cases

## After approval

Hand off to `frontend-driven-development:frontend-task-orchestration` to break work into tasks and dispatch subagents.
