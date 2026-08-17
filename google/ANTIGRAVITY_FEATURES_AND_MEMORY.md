# Google Antigravity Memory & Features Reference

Google Antigravity is an agentic engineering platform that unifies AI-assisted software development across four core surfaces: the **Antigravity IDE**, **Antigravity CLI (`agy`)**, **Antigravity Desktop (2.0)**, and the **Antigravity SDK**. 

---

## 🧠 Memory and Context Systems

Antigravity employs a multi-tiered context and memory architecture to persist knowledge, track tasks, and execute asynchronous workflows across sessions.

### 1. In-Context Working Memory
* **Gemini Context Window**: The immediate active state of the current conversation, containing recent user exchanges, files read, tool execution outputs, and model reasoning steps.
* **`/context` Command**: Displays active token usage, file buffers, and context filters.
* **`/btw` Command**: Injects lightweight context notes, developer corrections, or ephemeral guidance without derailing the main task planner.

### 2. Workspace Knowledge & Persistence
* **Knowledge Items (KI) System (`<appDataDir>\knowledge\`)**:
  * Curates localized, persistent knowledge summaries and repository-specific patterns to prevent redundant research.
  * Structure: `<appDataDir>\knowledge\<ki-id>\` contains `metadata.json` (summary, timestamps, references) and `artifacts/` (documentation, implementation notes).
  * System prompts inject KI summaries at conversation startup for instant relevant context retrieval.
* **Conversation Transcripts (`transcript.jsonl` & `transcript_full.jsonl`)**:
  * Stored in `...\brain\<conversation-id>\.system_generated\logs\`.
  * Chronological JSON Lines ledger of steps: `USER_INPUT`, `PLANNER_RESPONSE`, `MODEL`, `SYSTEM`, and tool calls.
  * `transcript.jsonl` contains token-efficient truncated content for fast scanning, while `transcript_full.jsonl` contains untruncated content.
* **Cross-Session Mention (`@mention`)**: Allows referencing prior Conversation IDs. Antigravity resolves the UUID, reads the historical transcript from that brain folder, and incorporates the relevant context.

### 3. Customizations & Project Configuration
Antigravity loads customizations from two roots: **Global** (`~/.gemini/config/`) and **Workspace** (`.agents/` in repository root).
* **Skills (`skills/<skill_name>/SKILL.md`)**: Reusable workflows loaded on demand with YAML frontmatter metadata.
* **Rules (`rules/`, `GEMINI.md`, `AGENTS.md`)**: Persistent styling guidelines, behavioral constraints, and instructions.
* **Plugins (`plugins/<plugin_name>/`)**: Namespaced bundles grouping skills, custom subagents, and MCP configurations.
* **Hooks & MCP Config**: Workspace hooks (`hooks.json`) and MCP servers (`mcp_config.json`).
* **Project JSON (`projects/<project-uuid>.json`)**: Configures repository folder URIs and safety gates: `fileAccessPolicy`, `internetPolicy`, `autoExecutionPolicy`, and `artifactReviewMode`.

### 4. Dynamic Subagent Memory & Background Messaging
* **Subagent Spawning (`invoke_subagent` / `browser_subagent`)**: Launches autonomous background agents for specialized tasks.
* **Workspace Isolation Modes**:
  * `inherit`: Subagent operates directly within the parent workspace directory.
  * `branch`: Creates an isolated workspace branch for risk-free builds, edits, or tests.
  * `share`: Shares worktree trees to allow concurrent tasks without duplicate disk space.
* **Messaging & Reactive Wakeup**: Communication between main agents, subagents, and background tasks uses a unified messaging bus. Agents wake up reactively on task completion without polling loops.
* **Scheduling (`schedule` tool)**: One-shot timers (with early termination conditions: `never`, `any`, `<sender-id>`) and recurring cron schedules for background polling.

---

## 🛠️ Complete Feature Set

### 1. Planning Mode & Artifacts
* **Implementation Plan (`implementation_plan.md`)**: Structured design doc created for complex tasks, requiring explicit user review and approval before execution.
* **Task Tracking (`task.md`)**: Dynamic checklist tracking subtasks, dependencies, and execution state.
* **Walkthrough (`walkthrough.md`)**: Visual summary generated after task completion with diffs, validation logs, and media artifacts.
* **Interactive Media**: Embedded screenshots, carousels, mermaid diagrams, and clickable file links (`file:///path/to/file#L1-L10`).

### 2. Terminal-Editor-Browser Automation
* **Command Execution (`run_command`)**: Proposes PowerShell/Bash commands executed within the workspace sandbox.
* **Task Manager (`manage_task`)**: Controls asynchronous processes via `list`, `kill`, `status`, and `send_input`.
* **Browser Automation (`browser_subagent`)**: Direct integration with Puppeteer, Playwright MCP, and Chrome DevTools to navigate, inspect DOM elements, capture screenshots, and debug live web apps.
* **Precision Code Editing**: Low-level regex search (`grep_search`), directory inspection (`list_dir`), file viewing (`view_file`), file creation (`write_to_file`), and surgical diff replacement (`replace_file_content`, `multi_replace_file_content`).

### 3. Slash Commands
* `/goal`: Activates thorough autonomous mode for long-running or overnight tasks.
* `/schedule`: Configures timers or recurring cron triggers for automated tasks.
* `/grill-me`: Triggers an interactive interview to clarify ambiguous requirements and align on plans.
* `/learn`: Persists user corrections or complex setups into long-term behavioral memory.
* `/btw`: Provides side-channel context notes without interrupting active planning.
* `/context`: Displays token metrics and context window occupancy.

### 4. Swarm Orchestration & CLI Tools
* **Swarm Dashboard**: Accessed via `npx antigravity-ide manager` to monitor subagent trees, token metrics, and active queues.
* **Workspace Sync**: `npx antigravity-ide .` scans workspace boundaries and validates `.agents/` configurations.
* **CLI Management (`agy`)**:
  * `agy changelog`: Displays framework updates.
  * `agy update`: Updates local agent engines and CLI binaries.
  * `agy --model [model_name]`: Switches the active Gemini model.
  * `npx antigravity-ide validate`: Runs diagnostic health checks on workspace permissions and MCP daemon ports.
