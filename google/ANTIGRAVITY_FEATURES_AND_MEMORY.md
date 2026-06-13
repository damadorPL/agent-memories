# Google Antigravity Memory & Features Reference

Google Antigravity is an agentic engineering platform that unifies AI-assisted software development across four core surfaces: the **Antigravity IDE**, **Antigravity CLI**, **Antigravity Desktop (2.0)**, and the **Antigravity SDK**. 

---

## 🧠 Memory and Context Systems

Antigravity employs a multi-tiered context and memory architecture to persist knowledge, track tasks, and execute asynchronous workflows across sessions.

### 1. In-Context Working Memory
* **Gemini Context Window**: The immediate active state of the current conversation, listing recent exchanges, files read, and tool execution logs.
* **`/context` Command**: Enables the developer to visualize token usage, active buffers, and search filters in the context window.
* **`/btw` Command**: Allows the developer to insert lightweight context notes, feedback, or quick comments without derailing the main task planner.

### 2. Workspace Knowledge & Persistence
* **Knowledge Store (`knowledge/`)**: Found in `~/.gemini/<tool-name>/knowledge/`, it caches local definitions and workspace locks (`knowledge.lock`) to verify environment status.
* **Conversation transcripts (`transcript.jsonl`)**: Stored in `...\brain\<conversation-id>\.system_generated\logs\transcript.jsonl`. This acts as a chronological ledger of user messages (`USER_INPUT`), agent thoughts (`PLANNER_RESPONSE`), tool invocations, and settings changes. Resuming a conversation re-reads the JSON Lines transcript to rebuild the agent's exact state.
* **Cross-Session Mention (`@mention`)**: Allows the developer to mention previous Conversation IDs. The agent resolves the UUID, reads the historical `transcript.jsonl` from the referenced brain directory, and injects a summary of that context into the active session.

### 3. Project Configuration Memory
* **Project JSON (`projects/`)**: Stored in `~/.gemini/config/projects/<project-uuid>.json`. This registers:
  * Workspace folders and repository paths (`folderUri`).
  * Safety and capability gates: `fileAccessPolicy` (allow/ask/deny), `internetPolicy`, `autoExecutionPolicy` (command automation settings), and `artifactReviewMode`.

### 4. Dynamic Subagent Memory
* **Subagent Spawning**: The primary agent can launch autonomous background subagents (e.g., `research`, `self`) via `invoke_subagent`.
* **Workspace Isolation Modes**:
  * `inherit`: Subagent runs directly in the parent directory.
  * `branch`: Creates an isolated directory branched from the parent, enabling risk-free compilation/testing.
  * `share`: Creates a shared folder worktree permitting concurrent work without duplicated disk storage.
* **Inter-Agent Inbox**: Subagents communicate asynchronously with each other and the parent via `send_message` commands.

---

## 🛠️ Complete Feature Set

### 1. Terminal-Editor-Browser Automation
* **Command Proposing**: Proposes cmd/PowerShell commands to the user, executing them inside a secure terminal sandbox once approved.
* **Browser Sandbox**: Direct integration with Puppeteer/Chrome DevTools and Playwright MCP servers, enabling the agent to load local web servers, inspect DOM trees, capture screenshots, and debug javascript errors in real-time.
* **Code Editor Tools**: Low-level search (`grep_search`), directory inspection (`list_dir`), and file editing (`replace_file_content`, `multi_replace_file_content`) tools.

### 2. Multi-Agent Swarm Orchestration
* **Swarm Dashboard**: Accessed via `npx antigravity-ide manager`, displaying a realtime swarm dashboard showing active subagents, running jobs, token graphs, and task queues.
* **DNA Integrity Sync**: Running `npx antigravity-ide .` scans workspace boundaries, global settings (`GEMINI.md`, `.agent/`), and auto-heals corrupted workspace files or broken dependencies.

### 3. CLI Management Commands
* `agy changelog`: Displays the developer changelog listing recent framework updates.
* `agy update`: Performs a secure update to the local agent DNA structure and CLI executable.
* `agy --model [model_name]`: Explicitly switches the underlying Gemini model model.
* `npx antigravity-ide validate`: Runs diagnostic health checks on workspace workspace permissions and MCP daemon ports.
