# OpenAI Codex Memory & Features Reference

OpenAI Codex CLI is a terminal-native, interactive coding agent. It uses an automated background memory system, hierarchical instruction files (`AGENTS.md`), local SQLite/file session persistence, and configuration utilities to maintain context across projects and terminal sessions.

---

## 🧠 Memory and Context Systems

Codex CLI structures its context using a combination of dynamic background memories, screen capture extensions, static rule files, and configuration managers.

### 1. Dynamic Memories (Codex Memories)
* **Storage Location**: Summarized interaction blocks are compiled locally under `C:\Users\<username>\.codex\memories\`:
  * `ad_hoc/`: Holds dynamic, session-specific facts and context snippets.
  * `rollout_summaries/`: Markdown logs of prior completed thread topics and decisions (e.g. `2026-05-22T13-57-59-...md`).
* **Background Consolidation**: Unlike real-time logs, Codex processes and consolidates memories asynchronously. When a thread has been idle for long enough, a consolidation model summarizes key takeaways, redacting secrets automatically.
* **Per-Thread Controls (`/memories`)**: Run within the TUI to toggle memory permissions for the active chat:
  * Toggle whether Codex can read past memories for context.
  * Toggle whether the current thread will be analyzed to generate future memories.

### 2. Static Instruction Hierarchy (`AGENTS.md`)
* **Purpose**: Markdown files (`AGENTS.md` and `AGENTS.override.md`) checked into repositories or home folders.
* **Hierarchical Merging**: At startup, Codex searches for instructions from the root downwards:
  1. Global instructions in `C:\Users\<username>\.codex\AGENTS.md`.
  2. Workspace root instructions (`<project-root>\AGENTS.md`).
  3. Working subdirectory level instructions (`<project-root>\subfolder\AGENTS.md`).
  * Files are merged root-to-leaf, so local subfolder rules override global/root definitions.
* **Override Mechanism**: `AGENTS.override.md` completely replaces `AGENTS.md` at the same folder level.
* **Size Limits**: `project_doc_max_bytes` (defaults to 32 KiB, configurable in `~/.codex/config.toml`).

### 3. Screen Context Memory (Chronicle)
* **Purpose**: An opt-in macOS research preview feature that captures screenshot OCR data, timing sequences, and active window states.
* **Storage**: Documented as `~/.codex/memories_extensions/chronicle/` (or locally `memories/extensions/`).
* **Privacy Controls**: Can be paused/resumed via menu bar options when handling credentials or sensitive windows.

### 4. Global Codex Home Directory Layout (`~/.codex/`)
On Windows (`C:\Users\<username>\.codex\`):
* **`memories/`**: Generated thread recall (`ad_hoc/`, `rollout_summaries/`).
* **`rules/`**: Global system rules (`default.rules`).
* **`sessions/`**: Chronological chat logs organized hierarchically by year and month (e.g. `sessions/2026/05/`).
* **`sqlite/`**: Session index databases (e.g., `codex-dev.db`).
* **`skills/`**: Installed capabilities and tool modules (`imagegen`, `skill-creator`, `openai-docs`).
* **`plugins/`**: Download cache and staging area for third-party extensions.
* **`vendor_imports/`**: Registry and caches for external tool integration.
* **`tmp/`**: Session state buffers and temporary execution artifacts.

---

## 🛠️ Complete Feature Set

### 1. Terminal UI (TUI) Session Commands
Starting the TUI with `codex` opens an interactive command shell. Inside, developers can run:
* `/clear`: Wipes active console buffers and resets current conversation states without affecting persistent memories.
* `/exit`: Exits the console safely.
* `/model`: Switches the target model mid-session (e.g., `/model gpt-5.5`).
* `/theme`: Previews, cycles, and saves color themes.
* `/memories`: Toggles memory reading and recording for the active session.
* `/skills`: Inspects and toggles available skills and tools.
* `/review`: Requests an automated review pass on recent code modifications.
* `/approval`: Configures permission modes for automated command execution.

### 2. CLI Invocation and State Commands
From the command line, users can execute:
* `codex "prompt"`: Runs a single-turn agent action immediately from the terminal.
* `codex resume`: Resumes a previous coding session, recovering filesystem status and history.
* `codex resume --all`: Opens an interactive selector displaying the history of local sessions.

### 3. Features Configuration Management
`config.toml` (stored under `~/.codex/config.toml`) configures feature behaviors:
* Enable memories by adding:
  ```toml
  [features]
  memories = true
  ```
* **CLI commands**:
  * `codex features list`: Displays all configuration toggle flags.
  * `codex features enable <feature>`: Activates a flag (e.g. `memories`).
  * `codex features disable <feature>`: Deactivates a flag.

