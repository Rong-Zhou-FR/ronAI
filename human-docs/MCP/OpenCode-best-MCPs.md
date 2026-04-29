### ⚠️ Preliminary Check: npm

```bash
# Install nvm if you don't have it
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Reload your shell
source ~/.bashrc

# Install Node.js 20 LTS
nvm install 20
nvm alias default 20
nvm use 20

# Verify
node --version   # Should show v20.x.x
```

### 🚀 Quick Install: All 3 Servers

This command sets up **Context Mode** (replaces sequential-thinking), **Context7**, and **Grep**:

```bash
# Install all NPM-based servers globally for stability
npm install -g context-mode @247arjun/mcp-grep @mseep/context7-mcp
```

If you are behind a proxy, set the environment variables **before** installing:

```bash
npm config set proxy http://proxy.company.com:8080
npm config set https-proxy http://proxy.company.com:8080
```

Add servers to your `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context-mode": {
      "type": "local",
      "command": "context-mode",
      "args": [],
      "enabled": true
    },
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "your-api-key-here"
      },
      "enabled": true
    },
    "mcp-grep": {
      "type": "local",
      "command": "/home/rongzhou/mcp-grep-shim",
      "args": [],
      "enabled": true,
      "timeout": 60000
    }
  }
}
```

For **Context7**, you need a free API key from [Context7](https://context7.com). For **mcp-grep**, first create the shim script:

```bash
cat > ~/.mcp-grep-shim << 'EOF'
#!/bin/bash
request=$(cat)
if echo "$request" | grep -q '"method":"prompts/list"'; then
  id=$(echo "$request" | grep -o '"id":[0-9]*' | grep -o '[0-9]*')
  echo '{"jsonrpc":"2.0","id":'"$id"',"result":{"prompts":[]}}'
elif echo "$request" | grep -q '"method":"resources/list"'; then
  id=$(echo "$request" | grep -o '"id":[0-9]*' | grep -o '[0-9]*')
  echo '{"jsonrpc":"2.0","id":'"$id"',"result":{"resources":[]}}'
else
  echo "$request" | mcp-grep
fi
EOF
chmod +x ~/.mcp-grep-shim
```

### 🔍 Verification

After starting OpenCode, ask your agent:

- *"Use ctx stats to see context savings"*
- *"Use context7 to search for FastAPI middleware documentation"*
- *"Use mcp-grep to find all email addresses in the docs folder"*

This setup equips your AI agent with context window optimization (98% reduction), up-to-date documentation, and powerful code search.
