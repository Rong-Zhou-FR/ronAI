# Task: Initialize Hierarchical AGENTS.md System

## Objective

Restructure the repository to support **high-quality agentic AI contribution** by replacing any existing single-instruction files (e.g., `copilot-instructions.md`, `.cursorrules`, `.github/copilot-instructions.md`) with a **hierarchical AGENTS.md system**.

The goal is to give AI agents precise, contextual, and domain-specific instructions at every level of the project.

---

## Problem
The current repo uses either:

- only a single root-level instruction file (e.g., `copilot-instructions.md`)
- or no copilot instructions at all

This approach is:
- Insufficient for growing projects
- Ambiguous for agents working in subdirectories
- Difficult for agents to interpret selectively
- Wasteful in large contexts
- Incompatible with monorepo-style scaling

Agents need **localized**, **hierarchical**, and **task-specific** context.

---

## Solution: Hierarchical AGENTS.md System

Implement a **hierarchical configuration system** where each directory provides its own contextual rules.

### Example target Structure
```
project-root/
├── AGENTS.md                          # Root-level project rules
├── src/                               # Main source code
│   ├── commands/                       # Command modules (example)
│   │   ├── AGENTS-[module].md          # Module-specific agent instructions
│   │   └── ...
│   └── services/
│       └── AGENTS.md                   # Service-layer context (optional)
├── docs/
│   └── man/                           # Documentation (reference from AGENTS files)
└── tests/
    └── AGENTS.md                       # Test-specific context (optional)
```

---

## TODO

### 1. Analyze the Project Structure
- Identify all major modules, commands, or functional units
- Locate existing documentation for each module
- Understand the tech stack, coding standards, and conventions
- Identify any existing instruction files (`copilot-instructions.md`, `.cursorrules`, etc.)

### 2. Create Root-Level `AGENTS.md`
Create `./AGENTS.md` with:

#### Required Sections:
- **Hierarchical Context Model** — Define context resolution order:
  1. Most specific `AGENTS.md` in current working directory (highest priority)
  2. Parent directory `AGENTS.md` files (walking up to root)
  3. Root `AGENTS.md` — global project rules (lowest priority)

- **Project Overview** — Brief description of the project's purpose and scope

- **Tech Stack** — Table of technologies, frameworks, libraries used

- **Language and Naming Conventions** — All naming rules, locale rules, alias conventions

- **Coding Guidelines** — Numbered list of coding rules specific to the project

- **Help Text Standards** (if CLI) — Rules for user-facing text

- **Database Optimization Standards** (if applicable) — SQL patterns, indexing rules

- **Module Registration Standards** (if applicable) — How new modules are registered

- **Commit Message Format** — Conventional commits or project-specific format

- **What to Avoid** — Explicit list of anti-patterns

- **Module-Level AGENTS Files** — Table listing all modules and their AGENTS files

- **Compatibility** — List compatible AI systems (Copilot, Cursor, Claude, Opencode, MCP)

- **Dependency and Inheritance Map** — ASCII diagram showing the hierarchy

#### Rule for Root AGENTS.md:
> Root AGENTS.md should NOT repeat module-level details. It provides global context only.

### 3. Generate Module-Level `AGENTS-[module].md` Files
For each major module/command in the project, create a corresponding file.

#### Location:
- For command modules: `<project>/src/commands/AGENTS-[command].md`
- For service modules: `<project>/src/services/AGENTS-[service].md`
- For other modules: Adjacent to the module files

#### Required Sections for Each File:
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
- CLI Options: [...]
- Subcommands: [...]
- Output format: [...]
- Side Effects: [DB writes, file I/O, network calls]

## Documentation Reference
- `[path/to/docs.md]`

## Domain-Specific Rules for Agents
- [How to modify this module safely]
- [Patterns to follow]
- [Integration points with other modules]
- [Migration rules for schema changes]
```

#### Rule for Module-Level Files:
> Module-level AGENTS files should NOT restate global rules. They focus on domain-specific behavior, constraints, and invariants.

### 4. Handle Sub-Typers / Subcommands
For commands that contain sub-typers (e.g., `retposto filtro`, `retposto subskribo`):
- Create separate `AGENTS-[subcommand].md` files
- Reference parent command's AGENTS file
- Document as standalone callable commands if registered in `pyproject.toml` or main entry point

### 5. Clean Up Old Instruction Files
After creating the new AGENTS.md system:
- Remove or archive old instruction files (`copilot-instructions.md`, `.cursorrules`, etc.)
- Update `.gitignore` if needed
- Note the change in commit message

---

## Requirements

### A. Hierarchical Context Model
Agents must follow this rule:

> When working inside a directory, load the nearest `AGENTS.md` file and merge it with parent `AGENTS.md` files up to root.  
> Local rules override global rules.  
> Most specific context wins.

### B. No Duplication
- Root AGENTS.md: Global rules only
- Module AGENTS files: Module-specific rules only
- No copying of global rules into module files

### C. Clarity and Determinism
All files must be:
- Declarative
- Concise
- Machine-readable
- Unambiguous
- Consistent in terminology

### D. Maximum Compatibility
The generated structure must work with:
- **GitHub Copilot** (reads AGENTS.md files automatically)
- **Claude / Cursor / Opencode** (hierarchical context loading)
- **MCP-based agents** (via context resolution protocol)
- **Any LLM-driven coding assistant** (declarative, machine-readable format)

---

## Deliverables

You must create/update to standard:

1. **Root `AGENTS.md`** — Canonical project-wide instruction file
2. **Module-level `AGENTS-[module].md` files** — One per major module/command
3. **Subcommand `AGENTS-[sub].md` files** — For sub-typers (if any)
4. **Updated documentation references** — Ensure all AGENTS files reference correct docs
5. **Dependency and Inheritance Map** — ASCII diagram in root AGENTS.md
6. **Compatibility note** — Confirmation of multi-agent system support
7. **Cleanup** — Removal of old instruction files
---


---

## Notes 

- Use the project's existing `copilot-instructions.md` (or equivalent), if present as the primary source for root AGENTS.md content
- Read each module's source code to understand its specific behavior before writing AGENTS files
- Read existing documentation (e.g., `docs/man/*.md`) to reference in AGENTS files
- Keep AGENTS files concise — aim for 30-80 lines per module file
- Use tables for structured data (tech stack, command lists, etc.)
- Include ASCII diagrams for dependency maps
- Test that the hierarchical loading works by simulating context resolution

---

## Commit Message
Use conventional commits format:
```
feat: implement hierarchical AGENTS.md system

- Create root AGENTS.md with global project rules
- Generate AGENTS-[module].md for all command modules
- Remove deprecated copilot-instructions.md
- Add compatibility notes for multi-agent systems
```
```

---

End of Prompt Template
```

---

## Usage Instructions

1. **Copy the prompt above** (from "Task: Initialize..." to "End of Prompt Template")
2. **Paste it to any AI agent** (Claude, Cursor, Opencode, Copilot Chat, etc.)
3. **The agent will**:
   - Analyze your project structure
   - Create the hierarchical AGENTS.md system
   - Clean up old instruction files
   - Provide a dependency map

---

## Customization

Before using the template, you may adjust:
- The target structure to match your project layout
- Additional sections needed for your tech stack
- Specific rules for your domain (e.g., security, compliance)
- The commit message format if different from conventional commits

---

## Verification Checklist

After the agent completes the task, verify:
- [ ] Root `AGENTS.md` exists and contains global rules
- [ ] All major modules have `AGENTS-[module].md` files
- [ ] Sub-typers have separate `AGENTS-[sub].md` files
- [ ] No duplication between root and module files
- [ ] Old instruction files are removed
- [ ] Dependency map is included in root AGENTS.md
- [ ] Compatibility note is included
- [ ] All AGENTS files reference existing documentation
