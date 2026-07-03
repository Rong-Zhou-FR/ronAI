# AGENTS.md — Root Project Rules for [Project Name]

This is the canonical, repo-wide instruction file for AI agents working on **[Project Name]**.

## Hierarchical Context Model

Agents **must** follow this rule:

> When working inside a directory, load the nearest `AGENTS.md` file and merge it with parent `AGENTS.md` files up to root.  
> Local rules override global rules.

Context resolution order (highest priority first):
1. `AGENTS-[module].md` in module directories — module-specific context
2. `AGENTS.md` in current working directory (if present)
3. Root `AGENTS.md` — global project rules

---
## Project Overview

**[Project Name]** is [1-2 sentence description].

[Optional: longer description, goals, target audience]

---

## Language and Naming Conventions

---

## Tech Stack

---
## Dependency management

This project uses poetry for dependency management and packaging.

## Coding Guidelines

1. **[Rule 1]** — [description]
2. **[Rule 2]** — [description]

## Documentation Standards

- **Every module must have a corresponding `AGENTS-[module].md` file.**
- **Every command must have documentation in `docs/man/[command].md`.**
- **Help text must include concrete examples.**
- **Options with restricted values MUST document all valid values.**

Format: `Example: --language fr` or `Example: -n 30,80`

---

## Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:`, `fix:`, `docs:`, `chore:`, `test:`, `refactor:`

---

## Testing Requirements

### Test Framework & Execution

| Aspect | Convention |
|--------|-----------|
| Framework | [test-framework, e.g. pytest/vitest] |
| Run all tests | [command, e.g. `pytest tests/`] |
| Run single test file | [command, e.g. `pytest tests/test_foo.py -v`] |
| Test directory | [path, e.g. `tests/`] |

### Testing Principles

1. **Test via the public API wherever possible.** Prefer integration tests over isolated unit tests. Mock external services (APIs, databases) only at system boundaries.
2. **Do not test directly via backend API alone — test through the user-facing interface** (CLI commands, GUI interactions). The end-to-end flow matters more than internal function coverage.
3. **When the project has a web UI, prefer automated E2E scripts over the interactive browser tool.** E2E scripts are fast, deterministic, and catch regressions without fragile session management. Use the interactive browser (`headed: true`) only as a last resort for manual debugging — in-flight tool calls are interrupted if the user types "continue" mid-action, leaving the browser in an inconsistent state.
4. **Console errors in browser tests indicate real bugs** — fix them even if tests pass. Common patterns: `TypeError: Cannot read properties of undefined`, `ReferenceError: Cannot access 'x' before initialization` (often a circular dependency in Svelte/Vue/React state).
5. **Every bug fix must include a test that would have caught the regression.**

### E2E / GUI Testing (web projects)

- Define the dev server start command and test data setup in the project's own `AGENTS.md` (or `scripts/AGENTS-scripts.md`).
- Existing E2E test scripts live in `[test-directory]` and should be the primary verification tool.
- When E2E tests do not yet exist, check the project's `AGENTS.md` or `scripts/AGENTS-scripts.md` for the test framework, dev server setup, and conventions. Create tests following the project's existing style.

---

## What to Avoid

- Do not use [deprecated libraries]
- Do not add [unnecessary dependencies]
- Do not [anti-pattern 1]
- Do not [anti-pattern 2]

---

## Module-Level AGENTS Files

The following module-specific AGENTS files are located in their respective directories:

| Module | AGENTS File | Documentation |
|---|---|---|
| [module1] | `AGENTS-[module1].md` | `docs/man/[module1].md` |
| [module2] | `AGENTS-[module2].md` | `docs/man/[module2].md` |

(Update this table as new modules are added)

---


## Dependency and Inheritance Map

```
Root AGENTS.md (global rules)
    │
    │
    └── Future: AGENTS.md in any subdirectory (local context)
```

Local rules override global rules. Module-level files focus on domain-specific behavior, constraints, and invariants.

