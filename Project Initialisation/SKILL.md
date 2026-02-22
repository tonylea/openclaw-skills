---
name: project-init
description: Use this skill when creating a new repository or project from scratch. Triggers include requests to initialise a project, set up a new repo, scaffold a codebase, or create the foundational structure for a new application. This skill covers repository structure, essential config files, GitHub settings, and CI pipeline skeleton. It ensures all other skills (atomic-commits, tdd, contributing) can function correctly from the first commit.
---

# Project Initialisation

This skill defines the standard setup when creating a new repository. It ensures the project is properly structured, documented, and configured so that the contributing, atomic-commits, and TDD skills work from day one.

## Repository Setup

### 1. Initialise with a clean first commit

```bash
git init
git add -A
git commit -m "chore: initial project scaffold"
```

The first commit should contain only the skeleton — no application logic. Application code arrives through the normal TDD/contributing workflow.

### 2. Essential files

Every new repository must include these files from the start. Do not skip any of them. Do not plan to "add them later."

| File | Purpose |
|---|---|
| `README.md` | Project name, description, prerequisites, setup, usage, how to test, licence |
| `CONTRIBUTING.md` | Branch naming, commit format, PR process, code style expectations |
| `.gitignore` | Correct ignore patterns for the stack (use gitignore.io or GitHub templates as a starting point, then review). Must include the secrets patterns from the `security-hygiene` skill |
| `.env.example` | Document every required environment variable with placeholder values. Never create `.env` with real values — see `security-hygiene` skill |
| `LICENCE` | Appropriate licence file for the project |

### 3. GitHub configuration files

Create a `.github/` directory containing:

**`.github/ISSUE_TEMPLATE/bug_report.md`**
```markdown
---
name: Bug Report
about: Report a bug
labels: bug
---

## Description

What happened?

## Steps to Reproduce

1.
2.
3.

## Expected Behaviour

What should have happened?

## Environment

- OS:
- Version:
```

**`.github/ISSUE_TEMPLATE/feature_request.md`**
```markdown
---
name: Feature Request
about: Suggest a new feature
labels: enhancement
---

## Description

What do you want and why?

## Acceptance Criteria

- [ ]
- [ ]
```

**`.github/PULL_REQUEST_TEMPLATE.md`**
```markdown
## What

Brief description of the change.

## Why

Link to issue or explanation of motivation.

## How

High-level approach taken.

## Checklist

- [ ] Tests added/updated
- [ ] Documentation updated if needed
- [ ] No unrelated changes included
- [ ] Self-reviewed the diff
```

### 4. Branch protection

Configure `main` branch protection (via GitHub settings or `gh` CLI):

- Require pull request reviews before merging
- Require status checks to pass before merging
- Require branches to be up to date before merging
- Do not allow force pushes to `main`
- Automatically delete head branches after merge

### 5. Versioning setup

Install and configure `commit-and-tag-version` for automated semantic versioning:

```bash
npm install --save-dev commit-and-tag-version
```

Add to `package.json` scripts:
```json
{
  "scripts": {
    "release": "commit-and-tag-version"
  }
}
```

This parses Conventional Commits to determine version bumps automatically. Do not create tags manually.

### 6. CI pipeline skeleton

Set up a minimal CI workflow that runs on every PR. The specifics depend on the stack, but at minimum it should:

- Install dependencies
- Run linting
- Run the test suite
- Report status back to the PR

Create `.github/workflows/ci.yml` with the appropriate steps for the project's language and tooling. The pipeline should block merging if any step fails.

## Directory Structure Principles

- Group by feature or domain, not by file type (e.g. `src/auth/` not `src/controllers/`, `src/models/`)
- Keep the root directory clean — config files only, application code lives in `src/` or equivalent
- Tests should live alongside the code they test (e.g. `src/auth/auth.test.ts`) or in a parallel `tests/` directory — be consistent, pick one approach and document it in CONTRIBUTING.md
- Create a `docs/` directory for any documentation beyond the README if needed

## Commit Sequence for Initialisation

Initialise the project as a series of atomic commits, not one giant commit. Do not bundle the entire scaffold into a single commit — that defeats the purpose of atomic commits from the very first interaction with the repo.

```
chore: initial project scaffold with package config
chore: add README with setup instructions
chore: add CONTRIBUTING guide
chore: add GitHub issue and PR templates
chore: configure gitignore for [stack]
chore: add CI pipeline
chore: configure commit-and-tag-version
```

Each commit leaves the repo in a valid state and can be reviewed independently.
