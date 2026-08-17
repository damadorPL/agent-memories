# Antigravity Conversation Matching and Compatibility

### 1. How Antigravity Matches Conversations and Brain Files

Every session is identified by a unique **Conversation ID (UUID)** (e.g., `df39273f-0772-4d21-a454-fd0a08263c7e`). This ID maps your session state to two separate structures in your App Data folder (`C:/Users/<username>/.gemini/`):

1. **The Conversation State File:**
   - **Path:** `C:/Users/<username>/.gemini/<tool-name>/conversations/<conversation-id>.[db|pb]`
   - **Purpose:** Stores the database structure or serialization of the chat history.
   - **Formats:**
     - `antigravity` (Desktop App) uses SQLite (`.db` files along with `-shm` and `-wal` transaction files).
     - `antigravity-cli` (CLI) uses Protocol Buffers binary files (`.pb`).
     - `antigravity-ide` (IDE Integration) uses a mix: older conversations are in Protocol Buffers (`.pb`), while newer updates utilize SQLite (`.db`).

2. **The Brain Directory:**
   - **Path:** `C:/Users/<username>/.gemini/<tool-name>/brain/<conversation-id>/`
   - **Purpose:** Holds markdown artifacts, temporary code files (scratch space), and raw conversation logs.
   - **Structure:**
     - `implementation_plan.md` & metadata
     - `task.md` & metadata
     - `walkthrough.md` & metadata
     - `.system_generated/logs/transcript.jsonl` & `transcript_full.jsonl` (step-by-step history of planner responses, tool calls, model thoughts, and user messages in JSON Lines format).
     - `scratch/` (temporary testing scripts).

---

### 2. Compatibility Matrix Across Tool Surfaces

| Component | `antigravity` (Desktop) | `antigravity-ide` (IDE) | `antigravity-cli` (CLI) | Cross-Tool Compatibility |
| :--- | :--- | :--- | :--- | :--- |
| **Brain Directory (`brain/`)** | Yes | Yes | Yes | **100% Compatible**: Identical folder layout, markdown artifacts, and JSON Lines transcripts. |
| **Conversation State (`conversations/`)** | SQLite (`.db`) | SQLite (`.db`) / Protobuf (`.pb`) | Protobuf (`.pb`) | **Conditionally Compatible**: Desktop and modern IDE can share SQLite files directly; CLI Protobuf states require export/migrator utilities. |
| **Customizations (`config/` & `.agents/`)** | Yes | Yes | Yes | **100% Compatible**: Shared global (`~/.gemini/config/`) and workspace (`.agents/`) skills, rules, plugins, and MCP configs. |
| **Knowledge Items (`knowledge/`)** | Yes | Yes | Yes | **100% Compatible**: Localized KI metadata and artifacts are interoperable across all surfaces. |

---

### 3. Migration and Cross-Tool Workflows

* **Desktop App & IDE:** Since both `antigravity` and newer `antigravity-ide` store active conversations as SQLite (`.db`) databases, they are directly compatible. You can copy/move the `<id>.db`, `<id>.db-shm`, and `<id>.db-wal` files along with the matching `brain/<id>` folder between them.
* **CLI to Desktop/IDE:** The CLI stores conversation states in `.pb` (Protobuf binary format). To move active conversations from `antigravity-cli` to `antigravity` or `antigravity-ide`, use built-in CLI export options (`agy export <id>`) or translation tools to migrate the conversation record into SQLite while linking the shared `brain/<id>` directory.
