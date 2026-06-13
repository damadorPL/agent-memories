# Antigravity Brain Layout & Configuration Reference

Antigravity maps conversations, brain folders, and chat history using a unique identifier called the **Conversation ID** (a UUID). Here is exactly how it is structured:

### 1. Conversation ID Matching
Every chat session (including main chats, subagent chats, and past conversations) is assigned a unique UUID, for example: `df39273f-0772-4d21-a454-fd0a08263c7e`. This ID links your active UI window to its underlying local storage.

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
  `...\brain\<conversation-id>\.system_generated\`

---

### 3. All Chat History (`transcript.jsonl`)
The entire step-by-step history of the conversation is stored in a file called **`transcript.jsonl`** located at:
```
C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\.system_generated\logs\transcript.jsonl
```
* **Format:** It is a JSON Lines (JSONL) file, where each line is a self-contained JSON object representing a "step" or action in chronological order.
* **Content:** It logs the full context of:
  * User inputs (`USER_INPUT`) and settings changes.
  * Model thoughts and planning (`PLANNER_RESPONSE`).
  * Tool calls (such as listing files or running tests) and their outputs.
  * System prompts (like knowledge artifacts loaded).

---

### 4. Project Configuration (`projects/`)
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

### 5. Plugins Directory (`config/plugins/`)
Ecosystem capabilities can be extended using **Plugins**, which bundle custom skills, configuration, and subagents. They are stored in:
```
C:\Users\<username>\.gemini\config\plugins\<plugin-name>\
```
* **Key Components:**
  * `plugin.json` / `gemini-extension.json`: Extension manifest specifying the plugin version, description, and author.
  * `skills/`: Custom tool and function definitions registered dynamically to extend the agent's toolset.
  * `agents/`: Custom subagent definitions configured to handle domain-specific workflows.

---

### Summary
When Antigravity starts or resumes a session:
1. It reads the local **Project Configuration** and matches the working workspace path.
2. It loads active **Plugins** from the configuration directories to register helper skills and subagents.
3. It resolves the **Conversation ID** to load the corresponding **`transcript.jsonl`** file from that ID's brain folder to reconstruct the chat history, and saves any new interactions, plans, or artifacts directly into that folder.