# Claude Code Memory & Features Reference

Claude Code is a terminal-native, CLI-based AI coding assistant. It uses a combination of session persistence, hierarchical rules files (`CLAUDE.md`), auto-generating memory files, MCP extensions, and interactive commands to maintain context across tasks.

---

## 🧠 Memory and Context Systems

Claude Code splits context management into local background sessions, repository-level static instruction files, and dynamic learning behaviors.

### 1. Session Memory & Transcripts
* **Storage Location**: Individual conversation turns are logged to `.jsonl` (JSON Lines) files stored under:
  ```
  C:\Users\<username>\.claude\projects\<encoded-project-path>\<session-uuid>.jsonl
  ```
* **Session Picking**: Claude retains history across terminal executions. You can list or resume previous sessions to preserve task context using the following CLI flags:
  * `claude --continue` (`claude -c`): Instantly resumes the last conversation session in the active directory.
  * `claude --resume` (`claude -r`): Opens a terminal-interactive menu showing all past sessions for the workspace.
  * `claude --resume <name/uuid>`: Resumes a specific session by its name or UUID.
  * `claude -p "prompt"`: Runs in non-interactive print mode for scripted or one-off tasks.

### 2. Static Authority Instructions (`CLAUDE.md` & `.claudeignore`)
* **Hierarchical Scope**:
  * **Global Rules:** Stored at `C:\Users\<username>\.claude\CLAUDE.md`. Applies across all projects on the machine.
  * **Project Rules:** Stored at `<project-root>\CLAUDE.md` or `<project-root>\.claude\CLAUDE.md`. Contains build commands, test instructions, code styling, and architectural rules.
* **Ignore Patterns (`.claudeignore`)**: Placed in project root to prevent Claude from indexing large data folders, build artifacts, or sensitive files.
* **Commands**:
  * `/init`: Scans the repository layout and automatically generates a boilerplate `CLAUDE.md` tailored to detected frameworks.
  * `/remember`: Promotes key constraints, commands, or patterns learned during a chat session to be appended directly into `CLAUDE.md`.

### 3. Dynamic Learning Memory (Auto-Memory)
* **Purpose**: Claude uses project memory records (e.g. under `.claude/` or `MEMORY.md`) to record project preferences, developer corrections, and codebase quirks learned during interactions.
* **Commands**:
  * `/memory`: Opens or updates the local project memory configuration files.

### 4. History Compaction & Token Preservation
* **`/compact`**: When a session grows long and begins to exceed token thresholds, `/compact` compresses past exchanges into a structured summary. This clears out verbose command outputs while keeping key decisions, preserving the context window limit.
* **`/context`**: Visualizes details about active token usage, file buffer counts, and overall context occupancy.
* **`/cost`**: Displays token costs and API spending for the current session.

---

## 🛠️ Complete Feature Set

### 1. Interactive CLI Slash Commands
Inside a running `claude` session, you can execute:
* `/resume`: Switches to a different conversation mid-session.
* `/rename`: Changes the name of the current session to a developer-friendly slug (e.g. `/rename payment-debug`), making it easy to identify in `--resume` lists.
* `/clear`: Starts a fresh conversation thread in the current workspace, resetting working memory while keeping persistent project settings.
* `/compact`: Triggers context compression to free token space.
* `/cost`: Inspects current session API usage and costs.
* `/doctor`: Runs self-diagnostic checks on environment health, network, and tool integrations.
* `/review`: Initiates code review workflows on uncommitted changes.
* `/pr_comments`: Fetches and reviews pull request review comments.
* `/bug`: Submits feedback or bug reports.
* `/search`: Performs a search on your filesystem.
* `/exit`: Safely terminates the interactive console.

### 2. Global CLI Utility Commands
* `claude config`: Reads or updates global configuration options (e.g., model selection, API tokens).
* `claude doctor`: Diagnoses system requirements, network latency, and MCP tool availability.
* `claude mcp`: Lists, adds, or configures Model Context Protocol (MCP) servers connected to Claude Code.

### 3. Autonomous Capabilities
* **File Operations**: Directly searches files, reads contents, and modifies directories.
* **Terminal Running**: Proposes compilation, linting, testing, and deployment commands, executing them on approvals.
* **Web Search**: Can query search engines to retrieve documentation, API specs, and package versions.
* **MCP Extensibility**: Connects to local and remote MCP servers for specialized tools and database querying.
