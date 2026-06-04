To benefit fully from the GitHub MCP server within OpenCode, you should understand the specific tools it provides and how to put them to work in your daily development workflows. The MCP tools bridge OpenCode's AI with a broad surface area of GitHub, letting you explore code, manage issues and pull requests, inspect Actions workflows, and more—all from your coding session.

## Default GitHub MCP Tool Categories

The GitHub MCP server exposes a curated set of tools that the AI agent can invoke on your behalf. These tools are organized by function:

### Code & Repository Navigation
* Search for and discover repositories by name or topic.
* Read file contents directly from a GitHub repository.
* Search code across repositories.
* List branches and commit history.
* Retrieve details of a specific commit.

### Issue Management
* List issues with filters (e.g., by state, label, assignee).
* Get details of a specific issue.
* Read issue comments.
* Search issues across repositories.

### Pull Request Management
* List pull requests with filters.
* Read detailed PR metadata.
* Search pull requests across repositories.

### Workflow Information (GitHub Actions)
* List available GitHub Actions workflows.
* Inspect recent workflow run history.
* View details of specific workflow runs.
* Retrieve job logs and full run logs.

### User Search
* Search for GitHub users by criteria.

These default tools are **read‑only** and optimized to minimize context window consumption so that they don't crowd out the rest of your conversation and code context.

## Enabling the Full Read‑Write Toolset

The default toolset focuses on reading data to save context. When you need to modify resources, you can explicitly ask the agent to enable all GitHub MCP tools for the session. Once activated, the following read‑write operations become available:

* Create issues, add labels, update status, and add comments.
* Create pull requests, request reviewers, merge/close PRs, and add PR comments.
* Create repositories, update repository settings, manage collaborators.
* Manually trigger workflow runs and cancel in‑progress runs.
* Create, edit, and delete labels and milestones.

Note that **write operations require user approval** the first time the model attempts to use them. You will be prompted to **allow the action at session level or per‑call level**, giving you control over what the agent can change.

## Practical Ways to Leverage GitHub MCP

Once configured, you benefit by integrating GitHub interactions directly into your OpenCode coding sessions. Here are some concrete use cases:

* **Explore a codebase quickly**: *“Search the repo for all references to `OAuthHandler` and show me the file where it’s imported.”*
* **Review and triage issues**: *“List all open issues labeled `bug` and summarize their titles.”*
* **Inspect CI/CD failures**: *“Get the logs for the most recent failed `ci` workflow run and identify the error.”*
* **Assist with code review**: *“Show me the diff and comments on PR #42.”*
* **Create and manage PRs**: *“Create a pull request from the branch `fix/auth-bug` with a description explaining the fix.”*
* **Update issues during development**: *“Add a comment on issue #18 with a link to the commit that fixes it.”*

These operations keep you in flow because you never leave your terminal—the LLM calls the tools based on natural language instructions.

## Monitoring and Troubleshooting

* **List available GitHub MCP tools**: `opencode mcp list` will show all tools provided by the `github` server.
* **Debug connectivity**: `opencode mcp debug github --log-level DEBUG` provides detailed logs if tools are not appearing.
* **Write operations not working**: Confirm you have enabled the full toolset for the session and that your PAT includes the necessary permissions (e.g., `Contents: Read and write`, `Pull requests: Read and write`, `Issues: Read and write`).

The official OpenCode documentation confirms that once an MCP server like GitHub is added, “MCP tools will automatically be made available to the LLM alongside the built-in tools”. This means there is **no special syntax** to invoke them—simply describe what you want to accomplish with GitHub, and the agent will select and use the appropriate tool.

## Token Usage Warning

The OpenCode docs caution that the GitHub MCP server “tends to add a large number of tokens and can easily exceed context limits”. To tackle the problem, we create a dedicated subagent for github-related tasks that can be invoked by primary agents.

Based on OpenCode's agent and MCP documentation, here is the recommended approach:

**Create a dedicated subagent with GitHub MCP access.** Configure your primary agent to invoke it via the `Task` tool. This isolates the MCP tools and their context cost to only when GitHub operations are needed, and prevents polluting the primary agent's context window. Primary agents cannot be called by other agents; only subagents are designed for delegation.

## Why This Works

*   **MCP servers are treated as tools** that you can enable or disable per agent. You can globally disable the GitHub MCP server and then enable it only in a specific agent.
*   **Only subagents can be invoked** by primary agents through the `Task` tool. Primary agents do not expose a callable interface to other agents.
*   **`permission.task` controls** which subagents an agent can invoke, using glob patterns.

## Step-by-Step Configuration

### 1. Globally Disable the GitHub MCP Server

Keep the server defined but disabled so it doesn't load tools for every agent:

```json
{
  "mcp": {
    "github": {
      "type": "remote",
      "url": "https://api.githubcopilot.com/mcp/",
      "enabled": false,
      "headers": {
        "Authorization": "Bearer {file:~/.config/opencode/.secrets/github-pat}"
      }
    }
  }
}
```

### 2. Create a GitHub Subagent

Create `~/.config/opencode/agents/github.md` (or `.opencode/agents/github.md` for project-level):

```markdown
---
mode: subagent
hidden: true
description: Handles GitHub operations like issues, PRs, and code search
tools:
  github: true
---
You are a GitHub assistant. Use the available GitHub MCP tools to help with repository tasks.
```

*   `mode: subagent` — makes it invokable by primary agents
*   `hidden: true` — keeps it out of the `@` autocomplete menu, reducing noise
*   `tools: github: true` — enables only the GitHub MCP server tools for this agent (the `github` key matches the name in your `mcp` config)

### 3. Allow the Primary Agent to Call It

In `opencode.json`, under your primary agent's configuration (e.g., Build), add:

```json
{
  "agents": {
    "build": {
      "permission": {
        "task": {
          "github": "allow"
        }
      }
    }
  }
}
```

This grants the Build agent permission to invoke the `github` subagent via the Task tool.

Now the primary agent will offload GitHub work to the dedicated subagent only when needed, keeping its own context lean.
