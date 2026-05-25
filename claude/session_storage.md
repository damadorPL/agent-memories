### 1. Project-to-Session Matching

Every Claude Code session is tied to the **absolute path of the working directory** where it was launched. The path is encoded into a folder name by replacing path separators (`\`, `/`) and colons (`:`) with dashes (`-`).

Example:
```
C:\Users\damador\Documents\Code\agent-memory
→ C--Users-damador-Documents-Code-agent-memory
```

---

### 2. Storage Location

All project session data lives under the user's home `.claude` directory:

```
C:\Users\<username>\.claude\projects\<encoded-project-path>\
```

Inside that folder:
* **Session files:** One `.jsonl` file per conversation, named with a UUID:
  `...\<encoded-path>\<session-uuid>.jsonl`
* **Memory / artifacts:** Subdirectories the user or agent creates (e.g., `memory\`):
  `...\<encoded-path>\memory\`

Git worktrees get their own separate entries because they have distinct absolute paths:
```
C--Users-damador-Documents-Code-portal--claude-worktrees-agitated-pascal
```

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

When Claude Code launches in a directory, it encodes the absolute path, locates the matching folder under `~/.claude/projects/`, and loads the most recent (or selected) `.jsonl` session file to reconstruct conversation history. New turns are appended to that file, and new conversations create a fresh UUID-named file in the same folder.
