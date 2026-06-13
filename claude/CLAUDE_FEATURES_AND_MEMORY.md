# Claude Code Memory & Features Reference

Claude Code is a terminal-native, CLI-based AI coding assistant. It uses a combination of session persistence, custom rules files, auto-generating memory files, and interactive commands to maintain context across tasks.

---

## 🧠 Memory and Context Systems

Claude Code splits context management into local background sessions, repository-level static instruction files, and dynamic learning behaviors.

### 1. Session Memory
* **Storage Location**: Individual conversation turns are logged to `.jsonl` (JSON Lines) files stored under `C:\Users\<username>\.claude\projects\<encoded-project-path>\`.
* **Session Picking**: Claude retains history across terminal executions. You can list or resume previous sessions to preserve task context using the following CLI flags:
  * `claude --continue`: Instantly resumes the last conversation session in the active directory.
  * `claude --resume`: Opens a terminal-interactive menu showing all past sessions for the workspace.
  * `claude --resume <name/uuid>`: Resumes a specific session by its name or UUID.

### 2. Static Authority Instructions (`CLAUDE.md`)
* **Purpose**: A versioned Markdown file (`CLAUDE.md`) stored in the project root containing absolute rules, build steps, test parameters, and coding styles. Claude reads this at startup.
* **Commands**:
  * `/init`: Scans the repository layout and automatically generates a boilerplate `CLAUDE.md` tailored to your detected framework.
  * `/remember`: Promotes key constraints, commands, or patterns learned during a chat session to be appended directly into `CLAUDE.md`.

### 3. Dynamic Learning Memory (Auto-Memory)
* **Purpose**: Claude uses a local `MEMORY.md` (or custom metadata files) to record project preferences, corrections, and lessons it learns about the codebase during interaction.
* **Commands**:
  * `/memory`: Opens or updates the local project memory configuration files.

### 4. History Compaction & Token Preservation
* **`/compact`**: When a session grows long and begins to exceed token thresholds, `/compact` compresses past exchanges into a structured summary. This clears out verbose command outputs while keeping key decisions, preserving the context window limit.
* **`/context`**: Visualizes details about active token usage, file buffer counts, and overall context occupancy.

---

## 🛠️ Complete Feature Set

### 1. Interactive CLI Commands
Inside a running `claude` session, you can use these interactive commands:
* `/resume`: Switches to a different conversation mid-session.
* `/rename`: Changes the name of the current session to a developer-friendly slug (e.g. `/rename payment-debug`), making it easy to identify in `--resume` lists.
* `/clear`: Starts a fresh conversation thread in the current workspace, resetting working memory while keeping the project's persistent settings.
* `/search`: Performs a search on your filesystem.
* `/exit`: Safely terminates the interactive console.

### 2. Autonomous Capabilities
* **File Operations**: Directly searches files, reads contents, and modifies directories.
* **Terminal Running**: Proposes compilation, linting, testing, and deployment commands, executing them on approvals.
* **Web Search**: Can query search engines to retrieve documentation, API specs, and package versions.
