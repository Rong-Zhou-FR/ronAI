How to set up Serena to work with OpenCode. Based on the Serena and OpenCode documentation.

### 🎯 Recommended Setup: Project-Based Configuration

Since you're working with **different project folders**, the key is to have Serena automatically detect and activate the correct project based on your current working directory. The `--project-from-cwd` option is perfect for this.

**How it works:**
*   You navigate into a project folder (e.g., `~/kodo/my-awesome-project`)
*   You launch OpenCode from within that folder
*   OpenCode starts Serena, which automatically detects the folder as the project root and activates it

### 📝 Step-by-Step Implementation

#### 1. Prepare Your Projects

Make sure each project directory in `~/kodo/` that you want Serena to work with is initialized as a *Serena project*:
```bash
# Navigate to your project
cd ~/kodo/your-project-name

# Create and index a Serena project (run from inside the project folder)
serena project create --index
```
You only need to do this once per project. Serena will create a `.serena/` directory inside your project.

#### 2. Configure OpenCode

Add the Serena MCP server to your **OpenCode configuration**. You have two options:

**Option A: Project-Specific Configuration** (Best for `~/kodo/`)
Create an `opencode.json` file at the root of the project(s) you want Serena to work with (e.g., in `~/kodo/your-project-name/opencode.json`). This keeps the configuration local to each project.

**Option B: Global User Configuration**
Add Serena to your global OpenCode config (usually located at `~/.config/opencode/config.json` or similar).

**For both options, add the following entry for Serena** (using the `local` type and the `--project-from-cwd` argument):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "serena": {
      "type": "local",
      "enabled": true,
      "command": ["serena", "start-mcp-server", "--project-from-cwd"],
      "timeout": 10000
    }
  }
}
```

**Explanation of the configuration:**
*   `type: "local"`: Tells OpenCode to run Serena as a local subprocess.
*   `command`: The exact command to start Serena. The `--project-from-cwd` flag is crucial—it makes Serena "look" at the current working directory from which OpenCode was launched.
*   `timeout: 10000` (10 seconds): Since Serena might need a moment to start up and index the project (for the first time), it's wise to increase the default timeout of 5 seconds.

#### 3. Launch and Use

1.  **Navigate to your project** in the terminal:
    ```bash
    cd ~/kodo/your-project-name
    ```

2.  **Launch OpenCode** from the same directory:
    ```bash
    opencode
    ```

3.  **Verify** the connection:
    *   In OpenCode, you can type `/mcp` or check the agent's tool list to see if Serena's tools (e.g., `find_symbol`, `find_referencing_symbols`) are available.
    *   If Serena's web dashboard is enabled (it is by default), it will open automatically in your browser, showing you which project is active and the server's logs.

### 📂 Workflow for Multiple Projects in `~/kodo/`

With this setup, your daily workflow is seamless:

1.  `cd ~/kodo/project-alpha`
2.  `opencode` → Serena automatically works on `project-alpha`
3.  Exit OpenCode
4.  `cd ~/kodo/project-beta`
5.  `opencode` → Serena automatically works on `project-beta`

**No need to change configurations** or manually tell Serena to switch projects. It all happens automatically based on your directory.

### ⚠️ Important Notes & Troubleshooting

1.  **First Use May Be Slower**: The first time Serena runs on a new project, it needs to index the entire codebase. This might take a few seconds. Subsequent startups will be much faster.

2.  **Context Limit Caution**: As the OpenCode docs mention, MCP tools add to the LLM's context. Serena has many tools. If you run into context limits, you can:
    *   Use OpenCode's global/agent-specific tool filtering (as shown in their docs) to enable only specific Serena tools.
    *   Look into Serena's `--mode` option to run it in a "lighter" mode.

3.  **Verify `serena` is in your PATH**: OpenCode will try to run the command `serena`. Make sure the Serena CLI is installed and available. You can test by running `which serena` in your terminal.

4.  **Alternative: Streamable HTTP Mode** (Not recommended for your use case):
    *   The OpenCode docs mention "remote" MCP servers. You could start Serena once as a persistent HTTP server (`serena start-mcp-server --transport streamable-http --port 9121`) and configure OpenCode to connect to it as a `remote` MCP server.
    *   **However**, the Serena docs explicitly warn: *"Serena is a stateful MCP server, and only one coding project can be active at a time."* This means the HTTP mode is only good if all your OpenCode windows/agents are working on **the exact same project**. For your multi-project workflow in `~/kodo/`, the `stdio` mode with `--project-from-cwd` is the correct and much more powerful approach.

### 🚀 Optional: Using `uvx` (if you don't want to install Serena)

If you prefer not to install Serena permanently, you can use `uvx` to run the latest version directly. Replace the `command` array in the OpenCode config:

```json
"command": ["uvx", "-p", "3.13", "--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server", "--project-from-cwd"]
```

*(Note: The Serena docs caution that this might be slower due to re-synchronization on each run).*

This configuration gives you a powerful, FOSS, and highly compatible semantic coding agent that works exactly as you need across all your projects in `~/kodo/`.
