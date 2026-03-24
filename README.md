# Frontend Driven Development

A Claude Code plugin that enforces disciplined frontend development. It classifies tasks by complexity, scans your codebase before making decisions, plans architecture with questions before writing code, breaks work into parallel tasks, verifies UI visually, and never lets you use `any` in TypeScript.

## How It Works

### Task Classification

Not every task needs the full pipeline. When the agent receives a task, it classifies it into one of three tiers before doing anything else:

| Tier | When | What Happens |
|------|------|-------------|
| **Tier 1 — Quick Fix** | 1-2 file change, unambiguous intent, no new components | Read file, make change, type check. Done. No scanning, planning, or orchestration. |
| **Tier 2 — Single Component** | One new/modified component, 1-3 files, one area of codebase | Reads existing context docs, asks 1-2 questions only if genuinely ambiguous, builds, runs verification loop. Skips full scan and planning. |
| **Tier 3 — Feature** | Multiple components, multiple codebase areas, new routing/state/auth | Full pipeline: git branch, codebase scan, architecture planning, user approval, API contracts, task breakdown, parallel subagents, full verification, bundle analysis, git commit/PR. |

The agent states its classification before starting so you can correct it. When unsure, it goes one tier up (over-preparing is safer than under-preparing). If a task turns out to be bigger than classified, the agent stops, reclassifies, and tells you before switching workflows.

### The Full Pipeline (Tier 3)

1. **Engineering standards always active** — senior frontend engineer identity, non-negotiable quality baseline, import rules, and "when to stop and ask" boundaries are loaded in every session regardless of tier.
2. **Git branch** — creates a feature branch following naming conventions before any code changes.
3. **Codebase scan** — checks git state, classifies codebase size (small/medium/large to control token budget), detects framework, architecture pattern, styling approach, testing setup, component patterns, and project conventions. Catalogues all dependencies by category. Runs Steps 1-3 in parallel for efficiency. Writes findings to `.context/project-profile.md`.
4. **Planning mode** — asks clarifying questions one at a time, proposes component architecture section by section, defines typed API contracts with mock data (including edge cases), checks consistency against codebase conventions, and waits for explicit user approval. Writes approved plan to `.context/implementation-plan.md`.
5. **Task breakdown + orchestration** — approved architecture is broken into tasks with a dependency graph. Independent tasks are dispatched to parallel subagents, each receiving persistent context docs, the relevant section of the plan, and inline testing guidance. Sequential dependencies are respected.
6. **Implementation loop** — every UI change passes through: build, TypeScript compilation + lint/test (run in parallel), screenshot verification loop, responsive check (3 breakpoints screenshotted in parallel), accessibility audit, and code review checklist.
7. **Bundle analysis** — production build, chunk size analysis, dependency audit (barrel imports, tree-shaking, duplicates, lighter alternatives), lazy loading check, image optimization.
8. **Git workflow** — commit hygiene, pre-push checks (types, lint, tests, no debug artifacts, no secrets), PR creation using project template.

### Reference Skills (loaded on demand)

- **TypeScript strictness** — `any` is banned. Proper types required: generics, discriminated unions, type guards, Zod for runtime validation.
- **Framework patterns** — React/Next.js, Vue, Svelte patterns based on what's installed.
- **Security** — XSS prevention, secure auth patterns, input sanitization, no secrets in client code.
- **Testing** — adapts to installed tools, covers E2E + unit + integration + accessibility testing.
- **Error recovery** — structured decision trees for six error categories with max 2 fix attempts before escalating.

### Persistent Context

The codebase scan and architecture skills write to `.context/project-profile.md` and `.context/implementation-plan.md`. All skills read these files before starting work — this is a global rule defined once in the engineering standards, not repeated per skill. Context survives across long sessions and between subagents. The codebase scan supports incremental re-scanning mid-session when dependencies or structure change.

### Token Optimization

The plugin is designed to minimize token usage on large codebases:

- **Codebase size classification** — small/medium/large determines glob depth limits, read line caps, and sampling strategies
- **Parallel steps** — codebase scan runs Steps 1-3 concurrently; workflow runs TypeScript + lint in parallel; responsive screenshots taken in parallel
- **Read limits** — components sampled at 50 lines, config files at 100 lines
- **No duplicate context loading** — defined once in engineering standards, not repeated in every skill
- **Merged code review** — review checklist is Phase 7 of the workflow, not a separate pass
- **Collapsed completion gate** — references earlier phases instead of re-listing all checks

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
| `frontend-engineering-standards` | Always-loaded: senior engineer identity, task classification, quality baseline, pipeline orchestration order, stop-and-ask boundaries |
| `codebase-scan` | Codebase size detection, git health check, framework/architecture/styling/testing detection, dependency cataloguing, convention file detection. Writes `.context/project-profile.md` |
| `frontend-architecture` | Planning gate: clarifying questions, component breakdown, API contracts, consistency check, user approval. Writes `.context/implementation-plan.md` |
| `frontend-task-orchestration` | Breaks approved architecture into tasks, builds dependency graph, dispatches parallel subagents with full context, reunification gate with bundle analysis and git workflow |
| `frontend-workflow` | Implementation loop: build → TypeScript + lint/test (parallel) → screenshot verify → responsive check (parallel) → accessibility audit → code review checklist → completion gate |
| `typescript-strictness` | Bans `any`, enforces generics/discriminated unions/type guards, compilation verification loop |
| `framework-patterns` | Framework-specific patterns (React/Next.js, Vue, Svelte) based on what's installed |
| `frontend-testing` | Adapts to installed test tools, covers E2E + unit + integration + accessibility testing |
| `frontend-security` | XSS prevention, secure auth patterns, input sanitization, secrets detection |
| `frontend-error-recovery` | Decision trees for type errors, build failures, runtime errors, styling issues, test failures, linter errors |
| `performance-bundle` | Production build analysis, chunk size thresholds, dependency audit, lazy loading check, image optimization |
| `git-workflow` | Branch naming, commit hygiene, pre-push checks, PR readiness with project template support |

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
- **Respect the token budget** — scan limits, parallel execution, and deduplication keep costs predictable on large codebases.

## Contributing

1. Fork the repo
2. Follow the skill structure (`skills/skill-name/SKILL.md`)
3. PRs welcome for new framework patterns, testing guidance, or workflow improvements

## License

MIT
