# Frontend Driven Development

A Claude Code plugin that enforces disciplined frontend development. It classifies tasks by complexity, scans your codebase before making decisions, plans architecture with questions before writing code, breaks work into parallel tasks, verifies UI visually, and never lets you use `any` in TypeScript.

## How It Works

### Task Classification

Not every task needs the full pipeline. When the agent receives a task, it classifies it into one of three tiers before doing anything else:

| Tier | When | What Happens |
|------|------|-------------|
| **Tier 1 — Quick Fix** | 1-2 file change, unambiguous intent, no new components | Read file, make change, type check. Done. No scanning, planning, or orchestration. |
| **Tier 2 — Single Component** | One new/modified component, 1-3 files, one area of codebase | Reads existing context docs, asks 1-2 questions only if genuinely ambiguous, builds, runs verification loop. Skips full scan and planning. |
| **Tier 3 — Feature** | Multiple components, multiple codebase areas, new routing/state/auth | Full pipeline: codebase scan, architecture planning, user approval, API contracts, task breakdown, parallel subagents, full verification and review. |

The agent states its classification before starting so you can correct it. When unsure, it goes one tier up (over-preparing is safer than under-preparing). If a task turns out to be bigger than classified, the agent stops, reclassifies, and tells you before switching workflows.

### The Full Pipeline (Tier 3)

1. **Engineering standards always active** — senior frontend engineer identity, non-negotiable quality baseline, import rules, and "when to stop and ask" boundaries are loaded in every session regardless of tier.
2. **Git health check + codebase scan** — checks git state (uncommitted changes, branch status, remote tracking), detects framework, architecture pattern (MVVM, MVC, Atomic, Feature-based, etc.), styling approach, testing setup, component patterns, and project conventions. Catalogues all dependencies by category so the agent uses what's installed instead of adding alternatives. Detects convention files (CLAUDE.md, .cursorrules, tsconfig, linter configs). Writes findings to `.context/project-profile.md` for persistent context.
3. **Planning mode** — asks clarifying questions one at a time, proposes component architecture section by section, defines typed API contracts with mock data (including edge cases), checks consistency against codebase conventions, and waits for explicit user approval before writing any code. Writes approved plan to `.context/implementation-plan.md`.
4. **Task breakdown + orchestration** — approved architecture is broken into tasks with a dependency graph. Independent tasks are dispatched to parallel subagents, each receiving the persistent context docs and relevant section of the plan. Sequential dependencies are respected.
5. **Implementation loop** — every UI change passes six phases: build, TypeScript compilation loop (max 3 retries), lint/test loop (max 2 retries), screenshot verification loop (max 3 retries with human escalation), responsive check (375px, 768px, 1280px), and accessibility audit.
6. **TypeScript strictness** — `any` is banned everywhere. Proper types required: generics, discriminated unions, type guards, Zod for runtime validation. Includes a standalone compilation verification loop.
7. **Security** — XSS prevention, secure auth patterns (httpOnly cookies, CSRF), input sanitization, no secrets in client code, dependency auditing.
8. **Code review + completion gate** — 8-category review checklist (accessibility, TypeScript, responsive, components, UI states, security, performance, bundle/dependencies). Mandatory 9-point completion gate must pass before any task is marked done.
9. **Error recovery** — structured decision trees for six error categories (type errors, build failures, runtime errors, styling issues, test failures, linter errors). Max 2 fix attempts per error before escalating to the user with diagnosis.

### Persistent Context

The codebase scan and architecture skills write to `.context/project-profile.md` and `.context/implementation-plan.md`. All downstream skills read these files before starting work, which means context survives across long sessions and between subagents. The codebase scan supports incremental re-scanning mid-session when dependencies or structure change.

### When the Agent Stops

The plugin defines explicit boundaries for when the agent should stop and ask rather than making autonomous decisions:

- Ambiguous design decisions with multiple reasonable approaches
- Scope expansion beyond the approved plan
- Conflicting project conventions
- Risky operations (deleting files, changing routing, modifying auth, installing large dependencies)
- Technical dead ends after 2 failed attempts

## Skills

| Skill | What It Does |
|-------|-------------|
| `frontend-engineering-standards` | Always-loaded: senior engineer identity, quality baseline, task classification, import rules, stop-and-ask boundaries |
| `codebase-scan` | Git health check, framework/architecture/styling/testing detection, dependency cataloguing, convention file detection, writes `.context/project-profile.md` |
| `frontend-architecture` | Planning gate: clarifying questions, component breakdown, API contract definitions, consistency check, user approval, writes `.context/implementation-plan.md` |
| `frontend-task-orchestration` | Breaks approved architecture into tasks, builds dependency graph, dispatches parallel subagents with context docs, reunification gate |
| `frontend-workflow` | Implementation loop: build, TypeScript compilation loop, lint/test loop, screenshot verification loop, responsive check, accessibility audit |
| `typescript-strictness` | Bans `any`, enforces generics/discriminated unions/type guards, compilation verification loop |
| `framework-patterns` | Framework-specific patterns (React/Next.js, Vue, Svelte) based on what's installed, dynamic import guidance |
| `frontend-testing` | Adapts to installed test tools, covers E2E + unit + integration + accessibility testing |
| `frontend-security` | XSS prevention, secure auth patterns, input sanitization, secrets detection, dependency auditing |
| `frontend-code-review` | 8-category review checklist + mandatory 9-point completion gate |
| `frontend-error-recovery` | Decision trees for type errors, build failures, runtime errors, styling issues, test failures, linter errors |

## Installation

```bash
# Coming soon to Claude Code plugin marketplace
# For now, install from git:
/plugin install path/to/frontend-driven-development
```

## Philosophy

- **Classify before acting** — not every task needs the full pipeline. Quick fixes should be quick.
- **Scan before deciding** — understand the codebase, dependencies, and conventions before proposing changes.
- **Plan before coding** — ask questions and design architecture up front. Define API contracts with typed interfaces.
- **Verify before claiming done** — type check, lint, test, screenshot, responsive check, accessibility audit. All must pass.
- **Detect conventions, don't assume them** — read the project's config files and existing code. Project rules override defaults.
- **Use what's installed** — if the project has a library for something, use it. Don't add alternatives or hand-roll solutions.
- **Strict types prevent entire categories of bugs** — `any` is never acceptable.
- **Security is not optional** — XSS, auth, input handling, secrets checked on every implementation.
- **Know when to stop** — explicit boundaries for when to ask the human instead of guessing.
- **Recover, don't retry blindly** — structured diagnosis for errors, not infinite retry loops.

## Contributing

1. Fork the repo
2. Follow the skill structure (`skills/skill-name/SKILL.md`)
3. PRs welcome for new framework patterns, testing guidance, or workflow improvements

## License

MIT
