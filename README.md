# Frontend Driven Development

A Claude Code plugin that enforces disciplined frontend development. It establishes senior engineer standards, scans your codebase before making decisions, plans architecture with questions before writing code, breaks work into parallel tasks, verifies UI visually, and never lets you use `any` in TypeScript.

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

1. **Every session loads engineering standards** — a senior frontend engineer identity and quality baseline are always active.
2. **Git health check + codebase scan** — checks git state, detects your framework, architecture pattern (MVVM, MVC, Atomic, etc.), styling approach, testing setup, dependencies, component patterns, and project conventions. Writes findings to `.context/project-profile.md` for persistent context.
3. **Planning mode** — asks clarifying questions one at a time, proposes component architecture, defines API contracts with typed interfaces and mock data, and waits for approval before writing any code. Writes approved plan to `.context/implementation-plan.md`.
4. **After approval: task breakdown** — work is split into tasks and dispatched to parallel subagents for efficient execution. Each subagent reads the persistent context docs.
5. **Implementation loop** — each piece of work follows: build, type check loop (max 3 retries), lint/test loop (max 2 retries), screenshot verify, responsive check, accessibility audit.
6. **TypeScript strictness enforced throughout** — `any` is banned, proper types are required everywhere.
7. **Security built in** — XSS prevention, secure auth patterns, input sanitization checked automatically.
8. **Frontend code review** — every piece of work is reviewed against an 8-category checklist covering accessibility, responsive, types, security, UI states, component patterns, performance, and bundle/dependency impact. Includes a mandatory completion gate.
9. **Error recovery** — structured decision trees for type errors, build failures, runtime errors, styling issues, test failures, and linter errors. Max 2 retries before escalating to the user.

## Skills

| Skill | What It Does |
|-------|-------------|
| `frontend-engineering-standards` | Always-loaded senior engineer identity and quality baseline |
| `codebase-scan` | Detects project framework, architecture pattern, styling, testing, component patterns before decisions |
| `frontend-architecture` | Plan mode: asks questions, proposes components, gets approval before code |
| `frontend-workflow` | Implementation loop: build, screenshot, responsive, accessibility |
| `typescript-strictness` | Bans `any`, enforces proper TypeScript patterns |
| `frontend-testing` | Adapts to installed test tools, covers E2E + unit + accessibility |
| `framework-patterns` | Framework-specific guidance based on what's actually installed |
| `frontend-task-orchestration` | Breaks work into tasks, dispatches parallel subagents |
| `frontend-security` | XSS prevention, secure auth patterns, input sanitization, no secrets in client code |
| `frontend-code-review` | Frontend-specific review checklist: accessibility, responsive, types, security, UI states, bundle impact |
| `frontend-error-recovery` | Structured diagnosis and decision trees for build, type, runtime, styling, test, and linter errors |

## Installation

```bash
# Coming soon to Claude Code plugin marketplace
# For now, install from git:
/plugin install path/to/frontend-driven-development
```

## Works with Superpowers

This plugin complements the [superpowers](https://github.com/jordanbarrand/superpowers) plugin. Superpowers handles general engineering process (TDD, debugging, code review). Frontend Driven Development handles frontend-specific discipline. They work together — superpowers' brainstorming and subagent skills are referenced where appropriate.

## Philosophy

- **Scan before deciding** — understand the codebase before proposing changes.
- **Plan before coding** — ask questions and design architecture up front.
- **Verify before claiming done** — screenshot, responsive check, accessibility audit.
- **Detect conventions, don't assume them** — read the project, don't guess.
- **Strict types prevent entire categories of bugs** — `any` is never acceptable.
- **Security is not optional** — XSS, auth, input handling checked on every implementation.

## Contributing

1. Fork the repo
2. Follow the skill structure (`skills/skill-name/SKILL.md`)
3. PRs welcome for new framework patterns, testing guidance, or workflow improvements

## License

MIT
