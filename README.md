# Antigravity Agent Memory & Compatibility Reference

Welcome to the **Agent Memory** reference repository. This repository serves as a knowledge base and technical documentation for understanding how the Antigravity ecosystem of AI tools (`antigravity` desktop, `antigravity-cli` CLI, and `antigravity-ide` IDE) handles conversation memory, directories, and database state formats.

---

## 📂 Repository Contents

This repository contains two main reference files:

1. **[antygravity.md](./google/antygravity.md)**: Details the internal folder layout, Conversation ID (UUID) matching, and the structure of brain files (artifacts, scratch spaces, and the JSON Lines `transcript.jsonl` chat log).
2. **[antigravity_compatibility.md](./google/antigravity_compatibility.md)**: Documents database/file formats across different versions (Desktop App vs. CLI vs. IDE Integration) and highlights cross-compatibility.

---

## 🧠 Brain Directory Layout

Every conversation session is assigned a unique **Conversation ID (UUID)** that links the active session to a local brain folder. On Windows, these files reside in:

`C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\`

Inside this folder, you will find:
* **Artifacts** (`implementation_plan.md`, `task.md`, `walkthrough.md`): Saved in the root.
* **Scratch Space** (`scratch/`): Contains temporary code or scripts run during execution.
* **Logs & Transcripts** (`.system_generated/logs/transcript.jsonl`): Chronological, line-by-line JSON record of user messages, thoughts, tool calls, and results.

---

## 🔄 Compatibility Overview

| Component | `antigravity` (Desktop) | `antigravity-ide` (IDE) | `antigravity-cli` (CLI) | Compatibility |
| :--- | :--- | :--- | :--- | :--- |
| **Brain Directory (`brain/`)** | Yes | Yes | Yes | **100% Compatible** (identical structure & markdown formats). |
| **Conversation State (`conversations/`)** | SQLite (`.db`) | SQLite (`.db`) | Protocol Buffers (`.pb`) | **Conditionally Compatible** (Directly compatible between Desktop & IDE; requires export/migrator tools for CLI). |

For more details on migration procedures and compatibility workarounds, see [antigravity_compatibility.md](./google/antigravity_compatibility.md).
