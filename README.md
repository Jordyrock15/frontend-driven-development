# Frontend Driven Development

A Claude Code plugin that enforces disciplined frontend development. It establishes senior engineer standards, scans your codebase before making decisions, plans architecture with questions before writing code, breaks work into parallel tasks, verifies UI visually, and never lets you use `any` in TypeScript.

## How It Works

1. **Every session loads engineering standards** — a senior frontend engineer identity and quality baseline are always active.
2. **Before any frontend work: codebase scan** — detects your framework, architecture pattern (MVVM, MVC, Atomic, etc.), styling approach, testing setup, and component patterns so decisions are grounded in what actually exists.
3. **Planning mode** — asks clarifying questions one at a time, proposes component architecture, and waits for approval before writing any code.
4. **After approval: task breakdown** — work is split into tasks and dispatched to parallel subagents for efficient execution.
5. **Implementation loop** — each piece of work follows: build, screenshot verify, responsive check, accessibility audit.
6. **TypeScript strictness enforced throughout** — `any` is banned, proper types are required everywhere.
7. **Security built in** — XSS prevention, secure auth patterns, input sanitization checked automatically.
8. **Frontend code review** — every piece of work is reviewed against a frontend-specific checklist covering accessibility, responsive, types, security, and UI states.

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
| `frontend-code-review` | Frontend-specific review checklist: accessibility, responsive, types, security, UI states |

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
