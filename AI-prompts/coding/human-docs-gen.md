# TEMPLATE: “Plan Mode” Prompt for ReadTheDocs Documentation Generation


## Objective
Analyse the repository structure and produce a complete, human‑oriented **ReadTheDocs documentation plan** (Sphinx‑style).  
The plan must describe what documentation should exist, where it should live, and how it should be structured.

## Tasks (Planning Only)
1. Inspect the repository layout.
2. Identify modules, packages, commands, or components that require documentation.
3. Propose a full Sphinx documentation structure:
   - required `.rst` files
   - directory layout
   - toctree hierarchy
   - sections (intro, install, usage, components, API overview, contributing, changelog)
4. Identify missing documentation.
5. Identify files that need rewriting or restructuring.
6. Identify which information should be extracted from code, README, manuals, or existing docs.
7. Add all required tasks to the todo list using the `todowrite` tool.

## Output Requirements
- Produce a **documentation plan only**.
- Include:
  - proposed file tree
  - proposed `.rst` pages
  - proposed section hierarchy
  - notes on content sources
  - tasks to be added to todo list
- Do **not** generate any documentation files yet.

## Constraints
- Use reStructuredText conventions.
- Keep the plan deterministic, concise, and actionable.


