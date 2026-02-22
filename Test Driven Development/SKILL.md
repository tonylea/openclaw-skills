---
name: tdd
description: Use this skill whenever the user wants to develop code using Test-Driven Development (TDD). Triggers include any mention of 'TDD', 'test-driven', 'red green refactor', 'write tests first', or when the user asks to build functionality incrementally with tests leading the design. Also use when the user asks to develop something 'the Kent Beck way' or mentions wanting micro-commits during development. This skill covers the full TDD cycle including the commit discipline — micro-commits at each green phase, squashed into atomic commits at the end of a development cycle.
---

# Test-Driven Development (TDD)

This skill implements strict TDD following Kent Beck's red-green-refactor discipline, with a commit strategy of micro-commits during development squashed into atomic commits before pushing.

## Core Principles

1. **Never write production code without a failing test.**
2. **Write only enough test code to produce a failure.**
3. **Write only enough production code to make the failing test pass.**
4. **Commit at every green phase.**
5. **Refactor only when tests are green.**
6. **Squash micro-commits into atomic commits at the end of the development cycle.**

## The TDD Cycle

Each unit of behaviour goes through these steps in strict order:

### Phase 1 — RED (Test Fails)

Write a single test for the next small piece of behaviour.

**Step 1a — Structural Red:** The test references a function, class, or module that does not yet exist. Run the test. It fails with an import error, `NameError`, or equivalent. This confirms the test is wired up correctly.

**Step 1b — Behavioural Red:** Create the minimal skeleton (function signature, class stub) so the test *runs* but fails on the assertion. This confirms the test is actually checking the intended behaviour, not just that something exists.

Both sub-steps matter. Step 1a catches wiring problems. Step 1b catches meaningless tests.

### Phase 2 — GREEN (Test Passes)

Write the **minimum** production code to make the failing test pass. Resist the urge to generalise, optimise, or handle cases not yet covered by a test. Ugly code is fine — you will refactor next.

**Once the test passes → COMMIT.**

```
git add -A && git commit -m "green: <short description of behaviour>"
```

### Phase 3 — REFACTOR

With all tests green, improve the code's structure without changing its behaviour. This includes:

- Removing duplication
- Improving naming
- Extracting functions or classes
- Simplifying conditionals

Run the tests after each change. If any test breaks, revert immediately to the last green commit.

**Once refactoring is complete and tests are still green → COMMIT.**

```
git add -A && git commit -m "refactor: <what was improved>"
```

### Then repeat from Phase 1 for the next behaviour.

## Commit Discipline

### During Development — Micro-commits

Commit at every green state. This gives you a safety net to revert to at any point. Commit messages during this phase are lightweight and follow a convention:

| Prefix       | When                                    |
|--------------|-----------------------------------------|
| `green:`     | A new test passes for the first time    |
| `refactor:`  | Code restructured, all tests still pass |
| `fix:`       | A broken test corrected during red phase|

Example sequence:
```
green: return empty list when no items
refactor: extract item validation to helper
green: filter items by status
green: raise ValueError on invalid status
refactor: replace if-chain with dict lookup
```

### End of Development Cycle — Squash to Atomic Commit

A "development cycle" ends when you have a complete, coherent unit of functionality — a feature, a bugfix, a behaviour. At that point, squash the micro-commits into one or more atomic commits that make sense in the shared history.

```bash
# Interactive rebase from the commit before you started this cycle
git rebase -i <commit-before-cycle>

# Squash all micro-commits into one (or a few logical) commits
# Write a proper commit message describing the completed behaviour
```

A good atomic commit message describes **what** the change does and **why**, not the TDD steps that produced it. The micro-commit history served its purpose during development and does not need to survive into the shared branch.

**Example:**

Before squash (local history):
```
green: return empty list when no items
refactor: extract item validation to helper
green: filter items by status
green: raise ValueError on invalid status
refactor: replace if-chain with dict lookup
green: add pagination support
```

After squash (shared history):
```
feat: add item filtering with status validation and pagination

Items can now be filtered by status (active, archived, pending).
Invalid status values raise ValueError with supported options listed.
Results are paginated with configurable page size (default 20).
```

## Practical Guidelines

### What counts as "the minimum code to pass"?

If the test expects `42`, returning `42` is legitimate. It feels wrong, but the next test will force you to generalise. Trust the process.

### When to write the next test

Ask: "What is the next simplest behaviour that the code does not yet support?" Start with degenerate cases (empty input, None, zero), then happy paths, then edge cases, then error handling.

### Test naming

Name tests after the behaviour they verify, not the implementation:

- Good: `test_returns_empty_list_when_no_items_match`
- Bad: `test_filter_function`

### When to revert vs. debug

If a refactor breaks a test and the fix is not immediately obvious (within ~60 seconds), revert to the last green commit rather than debugging. The micro-commit discipline makes this cheap.

### When the cycle feels slow

If TDD feels laborious, the steps are probably too large. Make the increments smaller, not bigger. Smaller steps mean faster feedback and fewer surprises.

## Workflow Summary

```
┌─────────────────────────────────────────────┐
│           DEVELOPMENT CYCLE                 │
│                                             │
│   ┌─────┐    ┌───────┐    ┌──────────┐      │
│   │ RED │───▶│ GREEN │───▶│ REFACTOR │──┐   │
│   └─────┘    └───┬───┘    └────┬─────┘  │   │
│                  │             │        │   │
│               commit        commit      │   │
│                  │             │        │   │
│                  ▼             ▼        │   │
│   ┌─────┐   micro-commits accumulate    │   │
│   │ RED │◀──────────────────────────────┘   │
│   └─────┘                                   │
│       │                                     │
│       ▼  (cycle complete?)                  │
│                                             │
│   ┌──────────────────────┐                  │
│   │ SQUASH & PUSH        │                  │
│   │ git rebase -i → atomic commit(s)        │
│   └──────────────────────┘                  │
└─────────────────────────────────────────────┘
```