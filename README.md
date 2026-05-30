# Agent Memory & Compatibility Reference

Welcome to the **Agent Memory** reference repository. This repository serves as a knowledge base and technical documentation for understanding how various AI assistant tools handle conversation memory, workspace directories, and session databases.

---

## 📂 Repository Contents

This repository contains the following reference files:

### Google Antigravity Ecosystem
1. **[antigravity.md](./google/antigravity.md)**: Details the internal folder layout, Conversation ID (UUID) matching, and the structure of brain files (artifacts, scratch spaces, and the JSON Lines `transcript.jsonl` chat log).
2. **[antigravity_compatibility.md](./google/antigravity_compatibility.md)**: Documents database/file formats across different versions (Desktop App vs. CLI vs. IDE Integration) and highlights cross-compatibility.

### Claude Code
3. **[session_storage.md](./claude/session_storage.md)**: Details project-to-session path encoding, home storage locations, and turn-by-turn chat history (`.jsonl`) files.

### OpenAI Codex
4. **[CODEX_PROJECT_MEMORY.md](./codex/CODEX_PROJECT_MEMORY.md)**: Explains how Codex handles project-specific conversation memory using repository files (e.g., `CODEX_PROJECT_MEMORY.md`).
5. **[CODEX_CLI_MEMORY_MODEL.md](./codex/CODEX_CLI_MEMORY_MODEL.md)**: Details Codex's hierarchical instruction model (`AGENTS.md`) and local background-generated Codex Memories/Chronicle screen context.
6. **[CODEX_MEMORIES_AND_CHRONICLE.md](./codex/CODEX_MEMORIES_AND_CHRONICLE.md)**: Focuses on background-generated Codex Memories configuration, CLI memory limits, and macOS Chronicle screen-capture requirements and security details.

---

## 🧠 Antigravity Brain Directory Layout

Every conversation session is assigned a unique **Conversation ID (UUID)** that links the active session to a local brain folder. On Windows, these files reside in:

`C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\`

Inside this folder, you will find:
* **Artifacts** (`implementation_plan.md`, `task.md`, `walkthrough.md`): Saved in the root.
* **Scratch Space** (`scratch/`): Contains temporary code or scripts run during execution.
* **Logs & Transcripts** (`.system_generated/logs/transcript.jsonl`): Chronological, line-by-line JSON record of user messages, thoughts, tool calls, and results.

---

## 🔄 Antigravity Compatibility Overview

| Component | `antigravity` (Desktop) | `antigravity-ide` (IDE) | `antigravity-cli` (CLI) | Compatibility |
| :--- | :--- | :--- | :--- | :--- |
| **Brain Directory (`brain/`)** | Yes | Yes | Yes | **100% Compatible** (identical structure & markdown formats). |
| **Conversation State (`conversations/`)** | SQLite (`.db`) | Mixed: Protocol Buffers (`.pb`) for older conversations, SQLite (`.db`) for newer | Protocol Buffers (`.pb`) | **Conditionally Compatible** (Directly compatible between Desktop & IDE; requires export/migrator tools for CLI). |

For more details on migration procedures and compatibility workarounds, see [antigravity_compatibility.md](./google/antigravity_compatibility.md).

---

## 🤖 OpenAI Codex Memory Hierarchy

Codex utilizes a multi-layered approach to context and memory preservation across sessions:

| Layer | Type | Target Path (Windows) | Function / Purpose |
| :--- | :--- | :--- | :--- |
| **`AGENTS.md`** | Static Instructions | `C:\Users\<username>\.codex\AGENTS.md` (Global)<br>`<project-root>\AGENTS.md` (Project) | Discovered hierarchically (root-to-leaf merges). Best for styling, rules, and commands. |
| **Project Memory** | Versioned Context | `<project-root>\CODEX_PROJECT_MEMORY.md` | Durable, manually updated markdown containing project goals, priorities, and known issues. |
| **Codex Memories** | Dynamic Recall | `C:\Users\<username>\.codex\memories\` | Background-generated summaries of prior threads. Managed via `/memories` command. |
| **Chronicle** | Screen Context | `C:\Users\<username>\.codex\memories_extensions\chronicle\` | Opt-in research preview (macOS only) summarizing recent active window context. |
