# Agent Memory & Compatibility Reference

Welcome to the **Agent Memory** reference repository. This repository serves as a knowledge base and technical documentation for understanding how modern AI coding assistants handle conversation memory, workspace directories, session persistence, instructions hierarchy, and cross-session context.

---

## 📂 Repository Contents

### Google Antigravity Ecosystem
1. **[antigravity.md](./google/antigravity.md)**: Details internal directory layout, Conversation ID (UUID) mapping, brain storage (artifacts, scratch spaces, `transcript.jsonl` / `transcript_full.jsonl`), Knowledge Items (`knowledge/`), customization roots, project configurations, and plugins.
2. **[antigravity_compatibility.md](./google/antigravity_compatibility.md)**: Documents database and file formats across different versions (Desktop App vs. CLI vs. IDE Integration), along with cross-tool compatibility and migration workflows.
3. **[ANTIGRAVITY_FEATURES_AND_MEMORY.md](./google/ANTIGRAVITY_FEATURES_AND_MEMORY.md)**: Comprehensive guide on how Google Antigravity uses memory (in-context, workspace knowledge, customizations, subagent workspaces) and details all major features (planning mode, browser sandbox, swarm orchestration, slash commands).

### Anthropic Claude Code
4. **[session_storage.md](./claude/session_storage.md)**: Details project-to-session path encoding, home storage locations (`~/.claude/`), and turn-by-turn chat history (`<session-uuid>.jsonl`) files.
5. **[CLAUDE_FEATURES_AND_MEMORY.md](./claude/CLAUDE_FEATURES_AND_MEMORY.md)**: Unified overview of Claude Code's memory (sessions, hierarchical `CLAUDE.md`, Auto-Memory `MEMORY.md`), CLI flags (`--continue`, `--resume`, `-p`), slash commands (`/compact`, `/doctor`, `/review`), and MCP extensions.

### OpenAI Codex
6. **[CODEX_PROJECT_MEMORY.md](./codex/CODEX_PROJECT_MEMORY.md)**: Explains how Codex handles project-specific conversation memory using repository files (e.g., `CODEX_PROJECT_MEMORY.md`).
7. **[CODEX_CLI_MEMORY_MODEL.md](./codex/CODEX_CLI_MEMORY_MODEL.md)**: Details Codex's hierarchical instruction model (`AGENTS.md` / `AGENTS.override.md`), document size limits, and local background-generated Codex Memories.
8. **[CODEX_MEMORIES_AND_CHRONICLE.md](./codex/CODEX_MEMORIES_AND_CHRONICLE.md)**: Focuses on background-generated Codex Memories configuration, CLI memory limits, security/redaction rules, and macOS Chronicle screen-capture requirements.
9. **[CODEX_FEATURES_AND_MEMORY.md](./codex/CODEX_FEATURES_AND_MEMORY.md)**: Detailed mapping of OpenAI Codex CLI features (TUI session commands, CLI state tools) and memory systems (Dynamic Memories, `AGENTS.md` instructions, Chronicle screen capture, `~/.codex/` directory layout).

---

## 📊 Cross-Ecosystem Architecture Comparison

| Feature / Dimension | Google Antigravity | Anthropic Claude Code | OpenAI Codex |
| :--- | :--- | :--- | :--- |
| **Primary Identifier** | Conversation ID (UUID) | Working Dir Encoded Path + Session UUID | Timestamped Sessions + SQLite DB |
| **Global Storage (Windows)** | `C:\Users\<user>\.gemini\` | `C:\Users\<user>\.claude\` | `C:\Users\<user>\.codex\` |
| **Project-Level Rules** | `.agents/rules/*.md`, `GEMINI.md`, `AGENTS.md` | `CLAUDE.md`, `.claude/CLAUDE.md`, `.claudeignore` | `AGENTS.md`, `AGENTS.override.md` |
| **Durable Knowledge** | Knowledge Items (`<appDataDir>\knowledge\`) | Auto-Memory (`MEMORY.md`, `.claude/`) | Codex Memories (`~/.codex/memories/`) & `CODEX_PROJECT_MEMORY.md` |
| **Transcript Format** | JSON Lines (`transcript.jsonl` & `transcript_full.jsonl`) | JSON Lines (`<uuid>.jsonl`) | Rollout Markdown + SQLite index |
| **Subagent / Multi-Agent** | Native autonomous subagents (`inherit`, `branch`, `share`) | Single agent loop with tool dispatches | Single agent loop with skills |
| **Browser Sandbox** | Puppeteer / Chrome DevTools / Playwright MCP | MCP-based browser tooling | MCP / Python tool environment |
| **Context Management** | Planning Mode artifacts, `/context`, `/btw`, `/compact` | `/compact`, `/context`, `/cost` | Consolidation background model, `/memories` |

---

## 🧠 Antigravity Brain Directory Layout

Every conversation session is assigned a unique **Conversation ID (UUID)** that links the active session to a local brain folder. On Windows, these files reside in:

`C:\Users\<username>\.gemini\<tool-name>\brain\<conversation-id>\`

Inside this folder:
* **Artifacts** (`implementation_plan.md`, `task.md`, `walkthrough.md`): Structured documents saved directly in the root.
* **Scratch Space** (`scratch/`): Contains temporary code or scripts run during execution.
* **Logs & Transcripts** (`.system_generated/logs/transcript.jsonl` & `transcript_full.jsonl`): Chronological, line-by-line JSON record of user messages, thoughts, tool calls, and results.

---

## 🔄 Antigravity Compatibility Overview

| Component | `antigravity` (Desktop) | `antigravity-ide` (IDE) | `antigravity-cli` (CLI) | Cross-Tool Compatibility |
| :--- | :--- | :--- | :--- | :--- |
| **Brain Directory (`brain/`)** | Yes | Yes | Yes | **100% Compatible** (identical structure & markdown formats). |
| **Conversation State (`conversations/`)** | SQLite (`.db`) | SQLite (`.db`) / Protobuf (`.pb`) | Protocol Buffers (`.pb`) | **Conditionally Compatible** (Desktop & IDE share SQLite; CLI Protobuf requires migrators). |
| **Customizations (`config/` & `.agents/`)** | Yes | Yes | Yes | **100% Compatible** (shared skills, rules, plugins, and MCP configs). |
| **Knowledge Items (`knowledge/`)** | Yes | Yes | Yes | **100% Compatible** (shared localized metadata and artifacts). |

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

