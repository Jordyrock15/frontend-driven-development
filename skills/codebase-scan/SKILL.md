---
name: codebase-scan
description: Use when starting any frontend work - detects project framework, styling, testing tools, component patterns, conventions, and dependencies before making architecture or implementation decisions. Writes findings to .context/project-profile.md for persistent context.
---

# Codebase Scan

**Purpose:** Single source of truth for project conventions. Run this before making any architecture or implementation decisions.

## Scan Modes

### Full Scan (default — run at session start)

Execute all steps below in order. Write output to `.context/project-profile.md`.

### Incremental Re-scan (run mid-session when things change)

Trigger when: a new dependency is installed, a new directory is created, or the user explicitly asks for a re-scan.

Instead of running all steps, only:
1. Re-read `package.json` for dependency changes
2. Glob for any new directories or config files
3. Update only the changed sections of `.context/project-profile.md`

This should take seconds, not minutes. Preserve existing sections that haven't changed.

---

## Full Scan Process

Execute Step 0 and Step 0.5 first (git check + size detection). Then run **Steps 1–3 in parallel** — they are independent reads and globs that don't depend on each other. Steps 4 and 5 depend on Step 3's directory results, so run them after.

### Step 0: Git Health Check

Before scanning the codebase structure, check the git state:

1. Run `git status -sb` — covers branch name, remote tracking status, uncommitted changes, and untracked files in one command
2. Run `git log --oneline -5` — get recent commit context

**Behavioural rules based on findings:**

- **Uncommitted changes:** Warn the user before starting work. Ask if they want to stash or commit first.
- **Branch is behind remote:** Flag it. Don't silently build on stale code.
- **On `main` or `master` directly:** Suggest creating a feature branch before making changes.
- **Never** force push, rebase, or alter git history without explicit user approval.

Include findings in `.context/project-profile.md` under a "Git State" section.

### Step 0.5: Large Codebase Detection

After the git health check, determine the codebase size:

1. Run `find src -type f | wc -l` (or the equivalent top-level source directory)
2. Check if `package.json` contains workspaces (monorepo signal)
3. Run `du -sh node_modules 2>/dev/null` to gauge dependency footprint

**Classification:**
- **Small** (<100 source files, single package): No restrictions. Run all steps normally.
- **Medium** (100–500 source files): Apply the limits described in each step below.
- **Large** (500+ source files or monorepo): Apply strict limits — glob depth capped at 3, read limits enforced, sampling mode for components and tests.

Record the classification in `.context/project-profile.md` under a "Codebase Size" section. All downstream steps reference this classification.

### Step 1: Read `package.json` and Catalogue Dependencies

Read `package.json` once. Extract `dependencies` and `devDependencies`, then detect and categorise in a single pass:

**Core stack detection:**
- **Framework:** react, next, vue, nuxt, svelte, angular, astro, solid-js, etc.
- **Styling:** tailwindcss, styled-components, @emotion/*, sass, less. Also check for CSS module usage in Step 4.
- **Testing:** playwright, vitest, jest, cypress, @testing-library/*.

**Dependency categories:**

| Category | Libraries to detect |
|---|---|
| **State management** | Zustand, Redux / @reduxjs/toolkit, Jotai, Recoil, MobX, Valtio, Pinia |
| **Data fetching** | @tanstack/react-query, SWR, @apollo/client, tRPC, ofetch |
| **Form handling** | react-hook-form, formik, @tanstack/react-form |
| **Validation** | zod, yup, joi, superstruct, valibot |
| **Animation** | framer-motion, motion, gsap, @react-spring/web, auto-animate |
| **UI component library** | @radix-ui/*, shadcn/ui, @mui/material, antd, @chakra-ui/react, @headlessui/react, @mantine/core |
| **Routing** | react-router-dom, @tanstack/react-router, next (App Router / Pages Router) |
| **Date handling** | date-fns, dayjs, luxon, @internationalized/date |
| **HTTP client** | axios, ky, ofetch, got |
| **Internationalization** | next-intl, react-i18next, @formatjs/intl |
| **Icons** | lucide-react, @heroicons/react, react-icons, @phosphor-icons/react |

**Rule: If a library exists in the project for a given concern, USE IT. Do not install alternatives or hand-roll solutions.**

### Step 2: Glob for config and convention files

Glob for all config and convention files in a single pass: `*.config.*`, `tsconfig.json`, `.eslintrc*`, `.prettierrc*`, `biome.json`, `postcss.config.*`, `tailwind.config.*`, `CLAUDE.md`, `.cursorrules`, `CONTRIBUTING.md`, `.editorconfig`.

From the files found, detect:

- **Linting/formatting:** ESLint (eslint.config or .eslintrc*), Prettier (.prettierrc* or prettier.config), Biome (biome.json)
- **Build tools:** Vite (vite.config), Next.js (next.config), Webpack (webpack.config), tsconfig paths/aliases
- **Styling (confirmation):** tailwind.config, postcss.config

Then read the relevant files and extract rules:

| File | What to extract |
|---|---|
| `CLAUDE.md` | All project-specific instructions |
| `.cursorrules` | Code style and behavior rules |
| `CONTRIBUTING.md` | Coding standards, PR conventions, style rules |
| `.editorconfig` | Indentation, line endings, charset |
| `.prettierrc` / `prettier.config.*` | Formatting rules (semicolons, quotes, trailing commas, print width) |
| `.eslintrc` / `eslint.config.*` | Linting rules and overrides |
| `tsconfig.json` | strict mode, paths, baseUrl, target, module resolution |
| `biome.json` | Formatting + linting rules |

**Read limits:** Read the first 100 lines of each config file. Key settings (strict mode, paths, formatting style) are always near the top. Exhaustive rule overrides in eslint/biome configs do not need to be catalogued — just note the tool is in use and capture any non-default settings visible in the first 100 lines.

**These project conventions take priority over this skill's default recommendations.** If a project's CLAUDE.md says to use a specific pattern, follow it even if it contradicts general best practices.

### Step 3: Glob for component directories and detect architecture

Search for: `src/components`, `src/app`, `src/pages`, `app/`, `pages/`, `src/features`, `src/ui`, `src/domain`, `src/application`, `src/infrastructure`, `src/views`, `src/viewmodels`, `src/models`, `src/controllers`, `src/containers`, `src/presenters`, `src/atoms`, `src/molecules`, `src/organisms`, `src/templates`.

Determine folder structure: feature-based, atomic, flat, or other.

Check for barrel exports (`index.ts` re-exports) in component directories.

Check for shared/common/ui component directories.

**Token limits:**
- Limit all globs to **depth 3** (e.g. `src/components/*/*` not deeper).
- If more than **50 directories** are found, stop enumerating. List only the top-level structure and sample 2-3 representative feature/component folders to determine the pattern.
- For barrel export detection, check **5 directories max** — just enough to confirm whether the convention exists.

**Architecture detection:** Based on the directories found, identify the pattern:

| Pattern | Detection signals |
|---|---|
| **MVVM** | `viewmodels/` or `*.viewmodel.ts` files, hooks that hold all logic, components are purely presentational |
| **MVC** | `controllers/`, `models/`, `views/` directories, route handlers with logic |
| **Feature-based / Vertical slices** | Each feature folder contains its own components, hooks, types, tests (e.g. `src/features/auth/`, `src/features/dashboard/`) |
| **Atomic Design** | `atoms/`, `molecules/`, `organisms/`, `templates/`, `pages/` directory structure |
| **Container/Presenter** | `containers/` and `components/` split, or `*.container.tsx` / `*.presenter.tsx` naming |
| **Colocation** | Everything for a feature lives together (component + test + styles + hooks in same folder) vs separated by type |
| **Clean Architecture** | `domain/`, `application/`, `infrastructure/` layers with clear dependency direction |

Report whichever pattern is detected. If mixed or unclear, note that. New components MUST follow the detected pattern — do not introduce a different architecture.

### Step 4: Read 3-5 representative components

Pick components from different directories. **Read only the first 50 lines of each component.** Conventions (naming, props pattern, imports, styling approach, logic placement) are virtually always visible in the top of the file. Only read beyond 50 lines if the pattern is genuinely unclear from the top.

For each, detect:

- **Naming convention:** PascalCase filenames? kebab-case?
- **Props patterns:** TypeScript interfaces vs type aliases?
- **Styling approach:** CSS modules (.module.css imports), Tailwind classes, styled-components, inline styles?
- **State management patterns:** React context usage, hooks, store imports?
- **Logic placement:** Where does business logic live? (in component, in hooks, in viewmodels, in services?)

### Step 5: Check test files

Use a **count-only glob** for `**/*.test.*`, `**/*.spec.*`, `**/__tests__/**` to determine test file volume. Do not enumerate every test file.

Then read **1-2 test files** to detect conventions (co-located vs `__tests__/` directories, render/screen/expect patterns, mocking approach).

**For large codebases (500+ files):** Only glob for test files within the directories already sampled in Steps 3 and 4. Do not scan the entire tree.

## Output

Summarize findings before proceeding. List what was detected for each category:

1. Git state (branch, cleanliness, remote status)
2. Framework and version
3. Styling approach
4. Testing tools and conventions
5. State management
6. Architecture pattern (MVVM, MVC, Feature-based, Atomic, Container/Presenter, Colocation, Clean Architecture)
7. Component patterns (naming, structure, props, logic placement)
8. Shared component library
9. Linting/formatting
10. Build tools and aliases
11. Key dependencies by category (from Step 1)
12. Project conventions (from Step 2)

Flag anything unusual or conflicting (e.g., both Jest and Vitest present, mixed naming conventions).

## Write `.context/project-profile.md`

After summarizing findings, write them to `.context/project-profile.md` in the project root. Create the `.context/` directory if it doesn't exist. Use this structure:

```markdown
# Project Profile

> Auto-generated by codebase-scan. Last updated: [date]

## Git State
- Branch: [current branch]
- Clean working tree: [Yes/No (details)]
- Behind remote: [Yes/No (details)]
- Recent commits:
  - [hash] [message]
  - ...

## Framework
[Framework and version]

## Architecture Pattern
[Detected pattern with evidence]

## Styling
[Styling approach and tools]

## State Management
[Libraries and patterns in use]

## Testing
[Testing tools, conventions, file locations]

## Key Dependencies
| Category | Library | Notes |
|---|---|---|
| [category] | [library] | [version/usage notes] |

## Component Patterns
- Naming: [convention]
- Props: [interface vs type alias]
- File structure: [colocation, barrel exports, etc.]
- Logic placement: [hooks, viewmodels, services, etc.]

## Project Conventions
[Rules extracted from CLAUDE.md, .cursorrules, CONTRIBUTING.md, tsconfig, linter configs, etc.]
[Note: these take priority over skill defaults]

## Linting & Formatting
[Tools and key rules]

## Build Tools
[Bundler, aliases, paths]

## Shared Components
[Discovered shared/reusable components and their locations]
```

This file serves as persistent context for all downstream skills. Keep it concise but complete.

## Greenfield Defaults

When nothing is detected (new project), default to:

- React 19 + Next.js (App Router)
- Tailwind CSS
- TypeScript strict mode
- Playwright (E2E) + Vitest (unit)
- Feature-based folder structure
