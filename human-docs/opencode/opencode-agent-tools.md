Based on the official OpenCode documentation, the coding agent has access to a specific set of built-in tools. These tools are the core actions the LLM can perform in your codebase:

| Tool Name | Description |
| :--- | :--- |
| **`bash`** | Execute shell commands (like `npm install` or `git status`) within your project environment. |
| **`edit`** | Modify existing files by replacing exact strings. This is the main way the LLM edits code. |
| **`write`** | Create new files or overwrite existing ones. Useful for generating new files from scratch. |
| **`read`** | Read file contents. It can also read specific line ranges to handle large files efficiently. |
| **`grep`** | Search file contents using regular expressions, allowing for fast and flexible codebase searches. |
| **`glob`** | Find files by matching patterns (e.g., `**/*.js`). It returns the paths of matching files. |
| **`lsp` (experimental)** | Interact with your configured Language Server (LSP) to get code intelligence, such as finding definitions, references, and call hierarchies. |
| **`apply_patch`** | Apply patch files to the codebase, a useful way to apply diffs from various sources. |
| **`skill`** | Load a `SKILL.md` file (a skill) and provide its content to the conversation. |
| **`todowrite`** | Manage and update a task list to track progress during complex, multi-step operations. |

### ⚙️ Controlling and Extending Tool Access

OpenCode offers several ways to manage or expand these capabilities:

*   **Permissions:** You can control how these tools behave. For each tool, you set it to `allow` (run automatically), `ask` (prompt you for approval), or `deny` (block the action). You can also set permissions for other actions, such as `websearch` and `webfetch`.
*   **Agents:** Different agents can have access to different tools. The default `build` agent has all tools enabled for full access, while the `plan` agent has restricted permissions for safe analysis.

## Custom tools

You can define your own functions that the LLM can call. These custom tools run alongside the built-in ones like `read` and `bash`. They are defined in TypeScript or JavaScript files.

## MCP based-tools

MCPs can provide new tools expanding capacity.

Below are some suggestions:

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

