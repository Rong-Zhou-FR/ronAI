The GitHub MCP server can be integrated with OpenCode as a remote server using a Personal Access Token (PAT). OpenCode v1.14.30 is the latest release as of this writing.

## Step-by-Step Setup

### 1. Create a GitHub Personal Access Token (PAT)
Create a fine-grained PAT with the necessary permissions:
- **Metadata**: Read-only
- **Contents**: Read and write
- **Pull requests**: Read and write
- **Issues**: Read and write

For scoped access, adjust permissions to the minimum required for your workflows.

### 2. Store the Token Securely
Store the token in a file with restricted permissions so OpenCode can reference it:

```bash
mkdir -p ~/.config/opencode/.secrets
chmod 700 ~/.config/opencode/.secrets
printf 'github_pat_xxxxxxxxxxxxxxxxxxxx' > ~/.config/opencode/.secrets/github-pat
chmod 600 ~/.config/opencode/.secrets/github-pat
```

Replace `github_pat_xxxxxxxxxxxxxxxxxxxx` with your actual token.

### 3. Configure OpenCode
Add the remote GitHub MCP server to your OpenCode configuration file at `~/.config/opencode/opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "github": {
      "type": "remote",
      "url": "https://api.githubcopilot.com/mcp/",
      "enabled": true,
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {file:~/.config/opencode/.secrets/github-pat}"
      }
    }
  }
}
```

This configuration:
- Uses the remote GitHub MCP server hosted at `api.githubcopilot.com`.
- Disables automatic OAuth negotiation (`"oauth": false`) and instead authenticates with the PAT stored in the secure file.
- The `{file:...}` syntax tells OpenCode to read the token from the specified path.

### 4. Verify Connectivity
Run these commands in your terminal to confirm the server is reachable:

```bash
opencode mcp list
opencode mcp debug github --log-level DEBUG
```

Then, test a real action inside OpenCode by asking it to perform a GitHub operation, such as:

> "List open pull requests in owner/repo using github mcp tools."

If this works, your setup is complete.

## Additional Notes
- The official OpenCode documentation confirms that remote MCP servers, including the GitHub MCP server, can be defined in the `mcp` section of the configuration and will be available alongside built-in tools.
- OpenCode’s remote MCP server options include `type`, `url`, `enabled`, `headers`, and `oauth`.
- If you prefer to run a local instance of the GitHub MCP server (e.g., via Docker), refer to the [GitHub MCP Server repository](https://github.com/github/github-mcp-server) for local setup instructions.

This setup follows a validated walkthrough tested with OpenCode 1.14.30.
