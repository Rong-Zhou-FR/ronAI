To share a system prompt across multiple repositories with PromptOpsKit, you first need to set up a central repository to act as the single source of truth. The approach you take depends on whether you want a single source of truth or prefer to keep your repositories more independent.

### 🗂️ Choosing Your Sharing Strategy

Here are the primary methods you can use, each with its own strengths for different workflows:

*   **Method 1: Git Submodule**
    *   **Best For**: Strict, version-controlled consistency where a single source of truth is required. Any change to the shared prompt needs to be explicitly updated across all projects.
    *   **Gist**: Embeds one Git repository inside another as a subdirectory.

*   **Method 2: NPM Package**
    *   **Best For**: An advanced, enterprise-level need to share prompts as a versioned, reusable dependency across many projects.
    *   **Gist**: Publish your prompts as a private NPM package that other projects can install.

### 🛠️ Step-by-Step Implementation (Git Submodule Method)

This is the recommended method for maintaining a single source of truth. We'll use a central `shared-system-prompts` repository.

#### Step 1: Create the Shared Prompts Repository

First, create a new repository on your Git host (e.g., GitHub) named `shared-system-prompts`. Clone it and add your shared assets.

1.  **Create the repository**: On your Git host (e.g., GitHub), create a new repository named `shared-system-prompts`.
2.  **Clone it locally**:
    ```bash
    git clone <your-shared-system-prompts-repo-url>
    cd shared-system-prompts
    ```
3.  **Add your shared system prompt file**. Create a directory called `shared` and add a file, for example, `shared/tone.md`. This file will hold your system instructions.

    ```yaml
    # shared/tone.md
    ---
    id: shared-tone
    schema_version: 1
    ---
    # System instructions
    You are a helpful, creative, and concise assistant. You always respond in a friendly but professional tone. When asked a question, you structure your answer for maximum clarity using bullet points and short paragraphs.
    ```
4.  **Commit and push**:
    ```bash
    git add shared/tone.md
    git commit -m "Add shared system prompt for tone"
    git push origin main
    ```

#### Step 2: Add Your Shared Repo as a Submodule

In each of your project repositories where you want to use this prompt, run the following command. We'll add the submodule to a path like `prompts/shared`.

```bash
# Run this from the root of your project
git submodule add <your-shared-system-prompts-repo-url> prompts/shared
git submodule update --init --recursive
```

This will add the contents of your `shared-system-prompts` repository into the `prompts/shared` directory in your project.

#### Step 3: Configure PromptOpsKit to Use the Shared Asset

Now, configure PromptOpsKit in your local project to discover the prompts. We'll do this through the `defaults.md` or a project-level configuration.

1.  **Create your local `prompts/defaults.md`**. If you don't have one, create it.

    ```yaml
    # prompts/defaults.md
    ---
    provider: openai
    model: gpt-4o
    includes:
      - shared/tone.md
    ---
    ```
2.  **Verify the `includes` path**: Make sure the path in the `includes` directive is relative to the `prompts/` directory. If your submodule is at `prompts/shared/` and the file is `prompts/shared/shared/tone.md`, the path would be `shared/shared/tone.md`. However, it's best to structure your submodule so the file is directly at `prompts/shared/tone.md`. You can adjust the submodule's directory structure to make this clean.

#### Step 4: Use the Shared Prompt in Your Code

You can now reference the shared prompt in your code just like any other prompt asset. The `includes` directive will automatically load and compose it into your final prompt.

```javascript
const kit = createPromptOpsKit({ sourceDir: './prompts' });

const result = await kit.renderPrompt({
  path: 'hello',
  environment: 'dev',
  variables: { name: 'World' }
});
// Your system prompt will now include the instructions from shared/tone.md
```

#### Step 5: Keeping the Shared Prompt Updated

When you update the prompt in your main `shared-system-prompts` repository, you need to update it in each project.

```bash
# In your project root
git submodule update --remote prompts/shared
git add prompts/shared
git commit -m "Update shared system prompt to latest version"
```

### 💡 Switching Providers or Models

To use a different provider or model for all prompts without changing individual files, just add a `defaults.md` file in the prompt directory. The settings will cascade to all prompts in that folder and its subfolders, so a single file can apply global overrides.

This approach ensures all your projects pull from the same, version-controlled source of truth for your AI's personality and core instructions.

If you run into any issues with the pathing or have questions about a different sharing method, feel free to ask.
