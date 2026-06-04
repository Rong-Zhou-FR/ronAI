Here are my recommendations for FOSS TUI/CLI LLM clients that speak the OpenAI protocol. I have ranked them by license freedom, feature set, and how naturally they fit your Mint environment.

---

## Top Recommendations

### 1. `tenere` – Simple, Vim-friendly, Rust + TUI
- **License**: GPL-3.0 ✅ (full copyleft)
- **Providers**: ChatGPT (OpenAI), llama.cpp, Ollama
- **Why**: Lightweight, fast (Rust), vim keybindings, actively maintained (commit Jan 2026). Minimal dependencies.
- **Caveat**: No custom `OPENAI_BASE_URL` – you use the official OpenAI endpoint or switch to the llama.cpp / Ollama backends for local models.

**Install** (cargo):
```bash
cargo install tenere
```
Then set your API key: `export OPENAI_API_KEY="sk-..."` and run `tenere`.  
Config file: `$HOME/.config/tenere/config.toml`

---

### 2. `kitty` (formerly `@norskhelsenett/kitty`) – Feature-rich, MIT
- **License**: MIT ✅
- **Providers**: ANY OpenAI-compatible endpoint (explicit `OPENAI_BASE_URL` support)
- **Why**: Polished Ink-based TUI, agent capabilities, reasoning/thinking display, model switching, plugin system. Actively maintained (Nov 2025).
- **Caveat**: Requires Node.js / npm.

**Install**:
```bash
npm install -g @norskhelsenett/kitty
```
Then point it anywhere that speaks the OpenAI protocol:
```bash
export OPENAI_BASE_URL="http://localhost:8080/v1"   # e.g., llama.cpp server
export OPENAI_API_KEY="not-needed"
kitty
```
See GitHub: https://github.com/NorskHelsenett/kitty

---

### 3. `meer` – Local-first, MIT, Node.js
- **License**: MIT ✅
- **Providers**: Ollama, OpenAI, Anthropic, Gemini, Hugging Face, vLLM, TGI
- **Why**: Local-first design, Ink TUI, diff previews, slash commands, file editing, git integration. Good documentation.
- **Caveat**: Node.js required; slightly heavier than Rust alternatives.

**Install**:
```bash
git clone https://github.com/meer-ai/meer
cd meer && npm install && npm run build && npm link
meer setup   # configures providers
meer          # launches TUI
```
Config: `~/.meer/config.yaml`

---

### 4. `ait` – Minimal, multi-provider, Rust
- **License**: Apache-2.0 ✅
- **Providers**: OpenAI, Anthropic, Cohere, Gemini, Groq, Ollama
- **Why**: Simple modal TUI, uses `ratatui` and `genai` crates, SQLite history.
- **Caveat**: Low community adoption (0 stars); last commit Sep 2024 (may be stale).

**Install**:
```bash
cargo install ait
```
Run: `ait` (reads `OPENAI_API_KEY` or `ANTHROPIC_API_KEY` env vars).

---

### 5. `gptui` – Python, mature, OpenAI-only
- **License**: MIT ✅
- **Providers**: OpenAI (only)
- **Why**: Feature-rich (file channels, plugins, voice, group talk), built with Textual. Good docs.
- **Caveat**: Python + Textual dependency; currently only supports OpenAI models (no custom base URL for local endpoints).

**Install**:
```bash
pip install gptui
```
Then run `gptui` after setting `OPENAI_API_KEY`.

---

### 6. `crush` (successor to OpenCode) – Powerful but not fully FOSS
- **License**: FSL-1.1-MIT ⚠️ (Functional Source License – restricts competitive use)
- **Providers**: OpenAI- and Anthropic-compatible APIs, custom base URL, LSP support
- **Why**: Extremely capable coding agent, backed by Charm ecosystem, multi-model, extensible.
- **Caveat**: Not OSI-approved; if you are a "FOSS absolutist" this may not qualify.

**Install**:
```bash
brew install charmbracelet/tap/crush   # or npm, AUR, Nix, etc.
```
Config supports `base_url` per provider.

---

## Which One Should You Pick?

| If you value... | Choose |
|-----------------|--------|
| Absolute FOSS + simplicity | `tenere` |
| Full OpenAI-compatible flexibility (custom base URL) | `kitty` |
| Local-first development with Git integration | `meer` |
| Minimal, multi-provider Rust TUI | `ait` |
| Python ecosystem / OpenAI-only | `gptui` |

All of the above run perfectly on Linux Mint with your hardware (Intel i3, 16 GB). The Rust binaries are lightweight; the Node.js ones are modest.

If you need more information about any of these projects, I suggest you check their respective GitHub repositories for the latest updates and installation instructions.
