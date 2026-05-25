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
     - `.system_generated/logs/transcript.jsonl` (contains the step-by-step history of planner responses, tool calls, and user messages in JSON Lines format).
     - `scratch/` (temporary testing scripts).

---

### 2. Compatibility Between Folders

* **Brain Directory (`brain/`):** **100% Compatible**. The structure of the `brain/<conversation-id>` directory is completely identical across all three tools (`antigravity`, `antigravity-cli`, and `antigravity-ide`). They all write identical markdown documents and standard JSON Lines transcripts. You can manually copy or link `brain` folders between these tool directories.
* **Conversation State (`conversations/`):** **Conditionally Compatible**.
  * **Desktop App & IDE:** Since both `antigravity` and newer `antigravity-ide` store active conversations as SQLite (`.db`) databases, they are compatible. You can copy/move the `<id>.db`, `<id>.db-shm`, and `<id>.db-wal` files along with the matching `brain/<id>` folder between them.
  * **CLI:** The CLI stores conversation states in `.pb` (Protobuf binary format). To move active conversations from `antigravity-cli` to `antigravity` or `antigravity-ide`, simple copying is not sufficient due to the format mismatch. However, you can use built-in CLI export options to move conversations into the desktop application, or use community migrators (like the *Antigravity IDE Migrator* or *Antigravity Decryptor*) to translate them.
