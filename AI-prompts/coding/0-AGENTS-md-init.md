## Objective
Set up a **complete, correct documentation structure** and **hierarchical AGENTS.md system** for this project based on `./dev/plans/0-init-plan.md` that we worked on together.

This ensures from day one:
- AI agents have precise, contextual instructions
- Documentation is complete and correctly structured
- The project scales without documentation debt
- Compatibility with all major AI coding assistants

---

## Todo now

### 1. Initialize Project Structure

### 2. Create Root-Level `AGENTS.md` FIRST

This is the foundational file. Create it **before writing any code**.

#### Required Sections:

```markdown
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
## MCP Tools Available

Agents have access to two MCP (Model Context Protocol) toolkits that MUST be used appropriately.

### Context-Mode Tools

**Purpose**: Execute code in sandboxed subprocesses and manage knowledge indexing to avoid loading large outputs into context.

**When to Use (Preferred over Bash/Read)**:
- API calls (gh, curl, aws), test runners (npm test, pytest), git queries (git log, git diff)
- Data processing, log file analysis, CSV/JSON analysis
- ANY CLI command that may produce large output (>20 lines)
- **Rule**: Only stdout enters context — raw data stays in subprocess

**Execution Tools**:
- `ctx_execute` - Run code (JS, Python, Shell, Ruby, Go, Rust, etc.) in sandbox. Output via console.log/print only
- `ctx_execute_file` - Process files without loading into context. File content in FILE_CONTENT variable
- `ctx_batch_execute` - Execute multiple commands + search in ONE call (replaces 30+ individual calls)

**Knowledge Management**:
- `ctx_index` - Index documentation/markdown into BM25 searchable knowledge base
- `ctx_search` - Search indexed content. Returns matching sections with full content
- `ctx_fetch_and_index` - Fetch URL, convert to markdown, index for searchable access
- `ctx_stats` - View context consumption statistics and token savings

**Maintenance**:
- `ctx_doctor` - Diagnose context-mode installation
- `ctx_upgrade` - Upgrade to latest version
- `ctx_purge` - Delete ALL session data (FTS5 DB, events, markdown) — destructive
- `ctx_insight` - Open analytics dashboard in browser

**Usage Pattern**:
1. Use `ctx_execute` with code that processes data and outputs only summary
2. Use `ctx_batch_execute` for multiple commands + queries in one call
3. Index docs with `ctx_index` or `ctx_fetch_and_index`, then search with `ctx_search`

### Context7 Tools

**Purpose**: Retrieve up-to-date documentation and code examples for programming libraries.

**Tools**:
- `context7_resolve-library-id` - Resolve library name to Context7 ID (e.g., `/vercel/next.js`, `/mongodb/docs`)
- `context7_query-docs` - Retrieve documentation and code examples. Call resolve-library-id first unless ID provided

**Usage Pattern**:
1. Call `context7_resolve-library-id` with library name and query
2. Use returned library ID in `context7_query-docs` with specific question
3. Retry with `researchMode: true` if initial query insufficient

### Serena Tools

**Purpose**: Symbolic code analysis and manipulation with deep codebase understanding.

**When to Use (Preferred for code operations)**:
- Finding symbols/classes/functions: `find_symbol`, `get_symbols_overview`
- Analyzing references: `find_referencing_symbols`
- Editing code: `replace_symbol_body`, `rename_symbol`, `insert_after_symbol`
- File operations: `read_file`, `create_text_file`, `replace_content`
- Project memory: `write_memory`, `read_memory` for persistent context

**Usage Pattern**:
1. Call `check_onboarding_performed` then `onboarding` if needed
2. Use symbolic tools (`find_symbol`) over text search when possible
3. Write important context to memory for persistence across conversations

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
```


### 4. Module-Level AGENTS.md Template

template for creating module-level AGENTS.md files:

```markdown
# AGENTS-[module].md — [Module Name] Agent Instructions

## Summary
[1-2 sentence description of the module]

## Purpose and Expected Behavior
[What it does, subcommands, key behavior patterns]

## Constraints and Invariants
- [Database constraints, security rules, invariants]
- [Dependencies that must be present]
- [State management rules]

## Input/Output Expectations

## Documentation Reference

## Domain-Specific Rules for Agents
- [How to modify this module safely]
- [Patterns to follow]
- [Integration points with other modules]
- [Migration rules for schema changes, if applicable]
```

### 5. Set up poetry for dependency management and project packaging

## Requirements

### A. Create AGENTS.md BEFORE Code
> The root `AGENTS.md` must be created **before** any substantial code is written.  
> This ensures the agent has context from the first line of code.

### B. Hierarchical Context Model
Specify in root-level ./AGENTS.md that agents must follow this rule:

> When working inside a directory, load the nearest `AGENTS.md` file and merge it with parent `AGENTS.md` files.  
> Local rules override global rules.  
> Most specific context wins.

### C. No Duplication
- Root AGENTS.md: Global rules only
- Module AGENTS files: Module-specific rules only
- No copying of global rules into module files

### E. Clarity and Determinism
All files must be:
- Declarative
- Concise
- Machine-readable
- Unambiguous
- Consistent in terminology

### F. Maximum Compatibility
The generated structure must work with:
- **GitHub Copilot** (reads AGENTS.md files automatically)
- **Claude / Cursor / Opencode** (hierarchical context loading)
- **MCP-based agents** (via context resolution protocol)
- **Any LLM-driven coding assistant**

---

## Deliverables

You must produce:

1. **Complete directory structure** — All folders created
2. **Root `AGENTS.md`** — Canonical project-wide instruction file
4. **Module-level `AGENTS-[module].md` templates** — Ready to fill for each module
6. **`.gitignore`** — Appropriate for the tech stack
7. **`README.md`** — Human-readable project overview
8. **Dependency and Inheritance Map** — ASCII diagram in root AGENTS.md


