To integrate PromptOpsKit with your OpenCode-based agentic coding workflow, you have three primary paths, each with different strengths. The key insight is that OpenCode was designed for exactly this kind of customization, offering several increasingly powerful integration points rather than a single rigid approach.

---

## Method 1: Quick Start with the `--system` Flag

The simplest integration is to use PromptOpsKit to render your shared prompt fragments locally, then pipe them directly into an OpenCode session using the `--system` flag, which overrides all other system prompts [11†L57-L60].

**Step-by-step:**

1. **Add the shared prompts repo as a submodule** in your project:
   ```bash
   git submodule add <shared-system-prompts-url> prompts/shared
   git submodule update --init --recursive
   ```

2. **Create a project-level `prompts/defaults.md`** that composes your fragments via `includes`:
   ```yaml
   ---
   provider: openai
   model: gpt-4o
   includes:
     - shared/tone/concise.md
     - shared/persona/assistant.md
     - shared/constraints/safety-baseline.md
   ---
   ```

3. **Render the composed system prompt** using PromptOpsKit:
   ```bash
   npx promptopskit render --format plain
   ```
   This outputs only the resolved system instructions as text.

4. **Pipe the rendered prompt into OpenCode**:
   ```bash
   opencode --system "$(npx promptopskit render --format plain)" "Implement a factorial function in Rust"
   ```

**Workflow tip:** Save steps 3–4 as a shell wrapper script (`oc-with-prompt`) to keep boilerplate out of sight.

This is ideal for experimentation — it requires zero OpenCode configuration changes.

---

## Method 2: Persistent Project-Level Configuration with AGENTS.md

For ongoing project work, you'll want the shared prompt fragments automatically injected into every session without manual steps. OpenCode's `AGENTS.md` mechanism is designed exactly for this: OpenCode discovers and includes these files in every AI interaction [11†L15-L18].

**Step-by-step:**

1. **Generate the AGENTS.md from PromptOpsKit** using a Git pre-commit hook or build step. Create a script (e.g., `scripts/generate-agents-md.sh`):

   ```bash
   #!/bin/bash
   # Render the composed system prompt and write it to AGENTS.md
   # AGENTS.md is wrapped with project-specific context
   {
     echo "# Project Guidelines"
     echo "## Shared System Instructions"
     npx promptopskit render --format plain
     echo ""
     echo "## Project-Specific Rules"
     echo "- Build: \`npm run build\`"
     echo "- Test: \`npm test\`"
     echo "- Lint: \`npx eslint .\`"
   } > AGENTS.md
   ```

2. **Make the hook executable** and register it:
   ```bash
   chmod +x scripts/generate-agents-md.sh
   # Link it as a pre-commit hook (or use husky)
   ln -s ../../scripts/generate-agents-md.sh .git/hooks/pre-commit
   ```

3. **Launch OpenCode normally** — the shared instructions are now part of its context:
   ```bash
   opencode "Add error handling to the auth service"
   ```

OpenCode discovers `AGENTS.md` by searching upward from the working directory, so placing it at the project root means it applies across the entire codebase [11†L19-L30]. You can also place package-level `AGENTS.md` files at `packages/opencode/AGENTS.md` for monorepos.

**Pro tip:** If you need per-directory behavior variants, render different compositions into different AGENTS.md files — one per package — and OpenCode's discovery logic will pick up the most local one.

---

## Method 3: Full Integration with Custom Agents (Most Powerful)

For the most granular control, define **custom OpenCode agents** that embed PromptOpsKit-rendered system prompts, tool permissions, and model preferences. OpenCode supports agents as Markdown files stored at `~/.config/opencode/agent/` or `.opencode/agent/` [17†L27-L30].

**Step-by-step:**

1. **Create a custom agent** using OpenCode's interactive wizard or by writing the Markdown directly. Using the CLI:
   ```bash
   opencode agent create
   ```
   This guides you through setting a system prompt and tool configuration [18†L13-L14].

   Alternatively, create the file manually at `.opencode/agent/custom.md`:

   ```markdown
   # Custom Coding Agent
   You are a senior software engineer. Follow these guidelines:

   ## Tone
   - Be concise and direct
   - Use precise technical language

   ## Behavior
   - Write tests for all new functions
   - Use TypeScript strict mode
   - Handle errors explicitly

   ## Tools
   - read, write, edit, apply_patch
   - bash
   - task
   ```

2. **Integrate PromptOpsKit rendering into agent creation.** Wrap your code's system prompt composition in a script (e.g., `scripts/compose-agent.sh`):

   ```bash
   #!/bin/bash
   # Render shared fragments and append project-specific rules
   npx promptopskit render --format plain
   cat .opencode/agent/project-specific.md
   ```

   Redirect the output of this script into the agent file to keep it always in sync with your shared fragments.

3. **Launch OpenCode with your custom agent:**
   ```bash
   opencode --agent custom "Refactor the payment module"
   ```
   Or set it as the default in your project's `.opencode/opencode.json`:
   ```json
   {
     "default_agent": "custom"
   }
   ```

OpenCode's prompt assembly stack places the custom agent's system prompt **after** environment information but **before** user overrides [11†L31-L37]. Combined with the `AGENTS.md` files described in Method 2, you get a layered prompt architecture where:

- Shared fragments (tone, persona, constraints) come **last** via AGENTS.md, serving as the behavioral foundation.
- Custom agent instructions come **first**, defining the task-specific role and tool access.

---

## Choosing Your Approach

| Method | Best for | Updates shared prompts |
|--------|----------|------------------------|
| `--system` flag | Experimentation, one-shot tasks | Manual per-command |
| `AGENTS.md` | Project-level conventions | Automatic via Git hook |
| Custom agent | Specialized workflows | Manual or scripted |

**Recommended starting point:** Begin with Method 2 (`AGENTS.md` generation) for your daily coding workflow. It's a one-time setup that pays off every session. For one-off experiments where you want to test a different prompt composition without committing it, fall back to Method 1's `--system` flag. Graduate to Method 3 when you need distinct tool permissions or model configurations for different task types (e.g., a code-review agent vs. a build agent).

---

## Going Further: MCP Integration

If you want to go beyond prompt composition and orchestrate OpenCode programmatically, you can wrap the entire toolchain in an MCP server. The `opencode-mcp` server exposes OpenCode commands as MCP tools, enabling any LLM client to programmatically execute OpenCode with PromptOpsKit-rendered prompts [7†L4-L14]. This would allow, for example, another AI agent to spawn an OpenCode coding sub-agent with your shared system prompt already composed. That path is more involved but opens the door to fully automated "agentic loops" where your shared prompt source of truth governs every sub-agent.
