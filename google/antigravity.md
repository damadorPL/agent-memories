# Antigravity Brain Layout & Configuration Reference

Antigravity maps conversations, brain folders, chat history, and configuration using a structured directory hierarchy. Here is exactly how it is structured:

### 1. Conversation ID Matching
Every chat session (including main chats, subagent chats, and past conversations) is assigned a unique **Conversation ID (UUID)**, for example: `df39273f-0772-4d21-a454-fd0a08263c7e`. This ID links your active UI window to its underlying local storage.

---

### 2. The "Brain" Folder Structure
Each conversation has a dedicated "brain" folder stored inside your local App Data Directory. On Windows, this directory is located at:
```
C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\
```
*(where `<tool-name>` is `antigravity`, `antigravity-ide`, or `antigravity-cli` depending on your environment)*

Inside this folder, the directory structure is organized as follows:
* **Artifacts:** Documents created or updated by the agent (e.g., `implementation_plan.md`, `task.md`, `walkthrough.md`) are saved directly in the root:
  `...\brain\<conversation-id>\<artifact_name>.md`
* **Scratch Space:** Temporary scripts and code files run during testing or debugging are located in:
  `...\brain\<conversation-id>\scratch\`
* **System Generated / Logs:** Internal log files and transcript files are located in:
  `...\brain\<conversation-id>\.system_generated\logs\`

---

### 3. Conversation Transcripts (`transcript.jsonl` & `transcript_full.jsonl`)
The entire step-by-step history of the conversation is stored under:
```
C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\.system_generated\logs\
```
* **`transcript.jsonl`**: Token-efficient version with large tool outputs truncated. Used as the primary fast-scanning log.
* **`transcript_full.jsonl`**: Complete, untruncated transcript containing full tool payloads and model thoughts.
* **Format:** JSON Lines (JSONL) format, where each line is a single JSON object representing one step in chronological order.
* **Step Types:**
  * `USER_INPUT`: Prompts, file attachments, and settings updates submitted by the user.
  * `PLANNER_RESPONSE`: Model thoughts, planning text, and tool calls.
  * `MODEL` / `SYSTEM`: System messages, knowledge items injected, and subagent notifications.
  * `USER_EXPLICIT`: Explicit actions or confirmations taken by the user.

---

### 4. Knowledge Items (KI) Architecture (`knowledge/`)
Antigravity maintains persistent, repository-specific knowledge items to avoid redundant research:
```
C:\Users\<username>\.gemini\<tool-name>\knowledge\
```
Inside this directory:
* **`<ki-id>/metadata.json`**: Summary, creation/update timestamps, and references to original code sources.
* **`<ki-id>/artifacts/`**: Detailed documentation, architectural notes, and implementation guidelines.
* **`knowledge.lock`**: Concurrency lock ensuring safe multi-process reads/writes.

---

### 5. Customization Roots & Discovery
Antigravity automatically discovers and loads customizations from two roots:
1. **Global Customizations Root:** `C:\Users\<username>\.gemini\config\`
2. **Workspace Customizations Root:** `<project-root>\.agents\`

Within these roots:
* **Skills (`skills/<skill_name>/SKILL.md`)**: On-demand workflow guides with YAML frontmatter metadata, helper scripts (`scripts/`), and examples (`examples/`).
* **Rules (`rules/*.md`, `GEMINI.md`, `AGENTS.md`)**: Style guides, behavioral constraints, and instructions merged hierarchically.
* **Plugins (`plugins/<plugin_name>/`)**: Namespaced bundles containing `plugin.json`, bundled skills, subagents, and MCP configurations.
* **Hooks (`hooks.json`)**: Lifecycle event triggers.
* **MCP Servers (`mcp_config.json` & `<appDataDir>\mcp\<serverName>\`)**: Model Context Protocol tool definitions and best practice guides (`instructions.md`).

---

### 6. Project Configuration (`projects/`)
Antigravity stores repository-specific configurations and permissions mapping workspaces to specific options under:
```
C:\Users\<username>\.gemini\config\projects\<project-uuid>.json
```
* **Format:** JSON configuration.
* **Key Fields:**
  * `id`: The unique project identifier.
  * `name`: The user-friendly project name or folder path.
  * `projectResources.resources`: Mappings of local paths/URIs associated with the workspace.
  * `settings`: Custom security policies applied to the agent session:
    * `fileAccessPolicy` (e.g., `AGENT_SETTING_POLICY_ALLOW`)
    * `internetPolicy` (e.g., `AGENT_SETTING_POLICY_ASK`)
    * `autoExecutionPolicy` (e.g., `CASCADE_COMMANDS_AUTO_EXECUTION_OFF`)
    * `artifactReviewMode` (e.g., `ARTIFACT_REVIEW_MODE_TURBO`)

---

### Summary
When Antigravity starts or resumes a session:
1. It reads the local **Project Configuration** and matches the working workspace path.
2. It loads active **Customizations & Plugins** from the global (`~/.gemini/config`) and workspace (`.agents/`) roots.
3. It loads relevant **Knowledge Items** from `<appDataDir>\knowledge\`.
4. It resolves the **Conversation ID** to load the corresponding **`transcript.jsonl`** file from that ID's brain folder to reconstruct the chat history, and persists any new interactions, plans, or artifacts directly into that folder.