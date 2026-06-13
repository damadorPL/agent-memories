### 1. Project-to-Session Matching

Every Claude Code session is tied to the **absolute path of the working directory** where it was launched. The path is encoded into a folder name by replacing path separators (`\`, `/`) and colons (`:`) with dashes (`-`).

Example:
```
C:\Users\damador\Documents\Code\agent-memory
→ C--Users-damador-Documents-Code-agent-memory
```

---

### 2. Storage Layout

The user's home `.claude` directory (`C:\Users\<username>\.claude\`) contains several distinct folders managing sessions, settings, and skills:

* **`projects/`**: The primary workspace-to-session mapping storage:
  `...\.claude\projects\<encoded-project-path>\`
  Inside each project directory:
  * **Session files:** One `.jsonl` file per conversation, named with a UUID: `...\<session-uuid>.jsonl`
  * **Memory / artifacts:** Subdirectories the user or agent creates (e.g., `memory/` or custom directories).
  * *Git Worktrees:* Get their own separate entries because they have distinct absolute paths, e.g., `C--Users-damador-Documents-Code-portal--claude-worktrees-agitated-pascal`
* **`session-env/`**: Stores environment state and temporary variables associated with specific active session UUIDs.
* **`sessions/`**: Stores process metadata (e.g., `<PID>.json`) tracking active instances of the tool.
* **`shell-snapshots/`**: Contains shell environment snapshots (e.g. `snapshot-bash-...sh`) used to restore terminal states or command history.
* **`skills/`**: Stores installed skills, custom tools, and automated agent scripts (e.g. `skill-creator`).
* **`transcripts/`**: Stores raw session activity logs and index mappings.

---

### 3. Session Files (`<uuid>.jsonl`)

Each `.jsonl` file stores the full turn-by-turn history of one conversation:

```
C:\Users\<username>\.claude\projects\<encoded-path>\<session-uuid>.jsonl
```

* **Format:** JSON Lines — each line is a self-contained JSON object representing one event.
* **Content:** User messages, assistant responses, tool calls and their results, system prompts.

---

### Summary

When Claude Code launches in a directory, it encodes the absolute path, locates the matching folder under `~/.claude/projects/`, and loads the most recent (or selected) `.jsonl` session file to reconstruct conversation history. New turns are appended to that file, and new conversations create a fresh UUID-named file in the same folder. Additional operational state (like shell history and environment flags) is loaded from companion directories in `~/.claude/`.
