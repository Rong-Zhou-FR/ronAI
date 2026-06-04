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

