---
name: git-workflow
description: Use during development to enforce consistent branching, commit hygiene, and PR readiness - covers branch naming, commit messages, when to commit, and pre-push checks
---

# Git Workflow

Enforces consistent git practices during frontend development. Use alongside other skills — this handles the version control layer.

## Branch Naming

Create a branch before making changes (unless the user has already created one).

**Format:** `{type}/{short-description}`

| Type | When |
|------|------|
| `feat/` | New feature or component |
| `fix/` | Bug fix |
| `refactor/` | Code restructuring without behaviour change |
| `chore/` | Dependencies, config, tooling |
| `style/` | Visual/CSS-only changes |

**Rules:**
- Lowercase, kebab-case: `feat/user-profile-card`
- Max 50 characters for the description part
- Never work directly on `main` or `master` — always branch first
- If the user has a ticket system integrated, include the ticket ID: `feat/PROJ-123-user-profile`

## When to Commit

Commit at natural completion points, not after every file save:

- **After completing a task** from the orchestration plan (types, a component, tests for that component)
- **After a working state** — code compiles, tests pass. Never commit broken code.
- **Before switching context** — if pivoting to a different part of the feature, commit current progress first
- **After fixing a bug** found during implementation — separate commit from the feature work

**Do NOT commit:**
- Half-finished components that don't compile
- Generated files, build artifacts, or `node_modules`
- `.env` files or secrets
- Temporary debug code (`console.log`, commented-out blocks)

## Commit Messages

**Format:**
```
{type}: {concise description}

{optional body — what and why, not how}
```

**Types:** `feat`, `fix`, `refactor`, `chore`, `style`, `test`, `docs`

**Rules:**
- Subject line: imperative mood, max 72 characters, no period
- `feat: add user profile card component` — not `feat: added the user profile card component.`
- Body explains **why** if it's not obvious from the subject
- One logical change per commit — don't bundle unrelated changes

## Pre-Push Checklist

Before pushing a branch (or before telling the user the branch is ready):

1. **TypeScript compiles** — `tsc --noEmit` passes
2. **Linter passes** — no errors (warnings acceptable if pre-existing)
3. **Tests pass** — all existing + new tests green
4. **No debug artifacts** — grep for `console.log`, `debugger`, `TODO: remove`
5. **No secrets** — grep for patterns like API keys, tokens, passwords in changed files
6. **Commits are clean** — logical units, descriptive messages, no "WIP" or "fix fix" commits
7. **Branch is up to date** — rebase on main if behind (ask user before rebasing)

## PR Readiness

When the user asks to create a PR or the work is complete:

1. Verify the pre-push checklist above
2. Ensure the branch has been pushed to remote
3. Check for a PR template at `.github/pull_request_template.md` or `.github/PULL_REQUEST_TEMPLATE.md`
   - **If one exists:** Use it. Fill in every section. Do not skip optional sections — either fill them or write "N/A" with a reason.
   - **If none exists:** Create `.github/pull_request_template.md` with the default template below, then use it for the PR.
4. Create the PR using `gh pr create`, populating the template with the actual work done

### Default PR Template

If the project has no PR template, create `.github/pull_request_template.md` with:

```markdown
## Summary

<!-- What does this PR do? Keep it to 2-3 sentences. -->

## Changes

<!-- Bullet list of what changed. Group by area if touching multiple parts. -->

-

## Type of Change

- [ ] New feature
- [ ] Bug fix
- [ ] Refactor (no behaviour change)
- [ ] Style/UI only
- [ ] Chore (dependencies, config, tooling)

## Testing

<!-- How was this tested? What should reviewers check? -->

- [ ] TypeScript compiles (`tsc --noEmit`)
- [ ] Linter passes
- [ ] Tests pass
- [ ] Visual verification at mobile, tablet, desktop
- [ ] Accessibility checked (keyboard nav, screen reader, contrast)

## Screenshots

<!-- Before/after if visual. Delete this section if not applicable. -->

## Notes for Reviewers

<!-- Anything the reviewer should pay attention to, trade-offs made, follow-up work needed. -->
```

## Rules

- **Never force push** without explicit user approval
- **Never rebase or amend** published commits without asking
- **Never commit to main/master** directly — always use a branch
- **Never include secrets** in any commit, even if the user asks — flag it instead
- If pre-commit hooks fail, fix the underlying issue — do not bypass with `--no-verify`
