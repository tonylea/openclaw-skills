# Project Standards (Always Active)

These rules apply to all repository work. Use the referenced skill for detailed guidance.

## Commits

Every commit MUST be atomic: one logical change, codebase functional, no mixed concerns. Use the `atomic-commits` skill at commit time.

**Commit message types by context:**
- **During a TDD cycle:** use the TDD skill's prefixes (`green:`, `refactor:`, `fix:`). At squash time, use Conventional Commits format.
- **All other commits:** use Conventional Commits format (`feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`).

## Branching and Workflow

Use trunk-based development with short-lived branches. Every piece of work links to a GitHub issue. Use the `contributing` skill for branching, PRs, reviews, and repo documentation standards.

## Security

Never commit secrets, credentials, or API keys. No exceptions, no "temporary" commits, no "I'll remove it later." Use the `security-hygiene` skill whenever working with configuration, environment variables, dependencies, or external service connections.

## New Projects

When creating a new repository, use the `project-init` skill to set up the correct structure, templates, and tooling before writing application code.
