
### Step 1: Ensure Your Environment is Ready
PromptOpsKit is an npm library, so you'll need **Node.js** (version 18 or later) and **npm** (version 9 or later) installed. The library is completely repo-native and does not depend on any hosted service.

### Step 2: Install PromptOpsKit Globally
Open your terminal and install the library globally to get access to its command-line interface (CLI).

```bash
npm install -g promptopskit
```

Alternatively, install it as a project dependency:

```bash
npm install promptopskit
```

### Step 3: Scaffold Your Prompt Assets
Use the CLI to generate a structured set of prompt directories and example files in your project.

```bash
npx promptopskit init
npx promptopskit skill
```

The `init` command creates the basic folder structure and example prompts, while `skill` deploys instruction files for AI coding assistants so they understand how to create and manage PromptOpsKit assets.

The scaffolding will create a directory structure resembling this:

```
prompts/
├── defaults.md         # Folder-level defaults (provider, model, system instructions)
├── hello.md            # Sample prompt with variables
├── hello.test.yaml     # Test sidecar with sample inputs and expected responses
└── shared/
    └── tone.md         # Shared system instructions (composed via 'includes')
tests/
└── hello.prompt.test.mjs  # Executable starter test for the hello prompt
```

### Step 4: Inspect & Understand the Prompt Asset Structure
Open `prompts/hello.md`. You'll see a Markdown file where the core configuration lives in the **YAML front matter** at the top, followed by standard Markdown headings (`# System instructions`, `# Prompt template`, etc.).

Example content of `hello.md`:

```yaml
---
id: hello
schema_version: 1
provider: openai
model: gpt-4o-mini
context:
  inputs:
    - name: name
      non_empty: true
---
# System instructions
You are a friendly assistant. Be warm and approachable.

# Prompt template
Hello, {{ name }}! How can I help you today?
```

### Step 5: Override Settings with `defaults.md`
`defaults.md` provides inherited settings for all prompts in its folder. Any prompt-local value will override the default.

Example `prompts/defaults.md`:

```yaml
---
provider: openai
model: gpt-4o
---

# System instructions
You are a helpful assistant.
```

### Step 6: Render a Prompt from the Command Line
Use the CLI's `render` command to preview how a prompt asset would be resolved. This automatically loads any sidecar test variables (e.g., `hello.test.yaml`).

```bash
npx promptopskit render hello --env prod
```

To pass custom variables manually, use the `--vars` flag:

```bash
npx promptopskit render hello --vars '{"name": "World"}'
```

### Step 7: Use the Library in Your JavaScript/TypeScript Code
To see how PromptOpsKit works at runtime, create a small test script (e.g., `test.mjs`) and run it with Node.js.

```javascript
import { createPromptOpsKit } from 'promptopskit';

const kit = createPromptOpsKit({ sourceDir: './prompts' });

const result = await kit.renderPrompt({
  path: 'hello',
  provider: 'openai',
  environment: 'dev',
  variables: {
    name: 'World'
  }
});

if (result.returnMessage) {
  console.log('Message:', result.returnMessage);
} else {
  console.log('Rendered request body:', JSON.stringify(result.request.body, null, 2));
}
```

Run it:

```bash
node test.mjs
```

The library renders a provider-specific request body (e.g., OpenAI Chat format) but leaves the actual HTTP call up to your own transport layer.

### Step 8: Explore the CLI's Other Capabilities
The CLI provides several other helpful commands:

*   **Validate**: Check your prompt files for schema errors.
    ```bash
    npx promptopskit validate ./prompts
    ```
*   **Compile**: Pre-compile your `.md` prompts into JSON or ESM modules for faster production loading.
    ```bash
    npx promptopskit compile ./prompts ./dist
    ```
*   **Inspect**: View a normalized version of a prompt's full resolved configuration as JSON.
    ```bash
    npx promptopskit inspect hello
    ```

### Next Steps
You are now set up to explore further. A logical progression from here would be to:
1.  **Create a custom prompt** for your own use case (e.g., email generation, code review).
2.  **Use the `includes` feature** to reuse shared tone or policy instructions from the `shared/` folder across multiple prompts.
3.  **Render prompts for different providers** like Anthropic or Gemini by simply changing the `provider` parameter in your render call, observing how the output body shape changes without altering your source prompt.

What use case are you thinking of tackling first with a custom prompt?
