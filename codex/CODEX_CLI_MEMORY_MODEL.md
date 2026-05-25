# Codex CLI Memory Model

Official Codex references:

- https://developers.openai.com/codex/memories
- https://developers.openai.com/codex/guides/agents-md
- https://developers.openai.com/codex/memories/chronicle

## Overview

Codex has two main ways to carry context across work:

1. `AGENTS.md` instruction files
2. Codex Memories

Use `AGENTS.md` for stable, required project guidance. Use Memories for generated recall from previous Codex threads.

## Layer 1: `AGENTS.md`

`AGENTS.md` is the static instruction layer.

Codex reads `AGENTS.md` files before doing work so each session can start with consistent context.

Good content for `AGENTS.md`:

- repository setup steps
- test commands
- lint commands
- coding style rules
- deployment rules
- team conventions
- important architecture notes
- safety or security requirements

`AGENTS.md` is best for guidance that should always apply.

## How Codex Discovers `AGENTS.md`

Codex builds an instruction chain when a run or TUI session starts.

Discovery order:

1. Global scope under the Codex home directory, usually `~/.codex`.
2. Project scope, starting from the project root and walking down to the current working directory.
3. Merge order from root to leaf, so more specific files appear later and can override earlier guidance.

Codex checks for:

```text
AGENTS.override.md
AGENTS.md
```

If `AGENTS.override.md` exists at a level, it takes precedence over the normal `AGENTS.md` at that same level.

On this Windows machine, the Codex home directory is typically:

```text
C:\Users\damador\.codex
```

So global guidance can live at:

```text
C:\Users\damador\.codex\AGENTS.md
C:\Users\damador\.codex\AGENTS.override.md
```

Project guidance can live in the repository:

```text
AGENTS.md
services\some-service\AGENTS.md
services\some-service\AGENTS.override.md
```

## `AGENTS.md` Limits

Codex skips empty instruction files.

Codex stops adding instruction files once the combined size reaches `project_doc_max_bytes`, which defaults to 32 KiB.

If instructions are truncated or missing:

- keep instructions concise
- move service-specific rules closer to the service directory
- raise `project_doc_max_bytes` in `~/.codex/config.toml`
- use fallback filenames only when needed

Example config:

```toml
project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
project_doc_max_bytes = 65536
```

## Layer 2: Codex Memories

Codex Memories are the generated memory layer.

After Memories are enabled, Codex can turn useful context from eligible prior threads into local memory files. Future sessions can then use those memories as context.

Memories are useful for:

- recurring user preferences
- repeated project workflows
- tech stacks
- project conventions noticed over time
- known pitfalls from earlier sessions

Memories should not be the only place for required project rules. Required rules belong in `AGENTS.md` or checked-in documentation.

## Enable Memories

Memories are off by default.

Enable them in Codex settings, or add this to `~/.codex/config.toml`:

```toml
[features]
memories = true
```

## Memory Storage

Codex stores memory files under the Codex home directory.

Default path:

```text
~/.codex/memories/
```

On this Windows machine:

```text
C:\Users\damador\.codex\memories
```

The docs describe these files as generated state. They may include summaries, durable entries, recent inputs, and supporting evidence from prior threads.

You can inspect them when troubleshooting, but the primary control surface should be Codex settings, `/memories`, and project documentation, not manual editing.

## How Memory Generation Works

Codex generates memories in the background.

Important behavior:

- active or short-lived sessions may be skipped
- memory updates may not happen immediately after a thread ends
- Codex waits until a thread has been idle long enough before summarizing it
- memory generation can skip a pass when remaining Codex rate limit is below the configured threshold
- Codex redacts secrets from generated memory fields

## Per-Thread Memory Controls

Use this command in the Codex app or TUI:

```text
/memories
```

This controls memory behavior for the current thread.

Thread-level choices can decide whether:

- this thread may use existing memories
- this thread may be used to generate future memories

Thread-level settings do not change global memory settings.

## Important Memory Config Keys

Common memory settings include:

- `memories.generate_memories`: controls whether new threads can become memory-generation inputs
- `memories.use_memories`: controls whether existing memories are injected into future sessions
- `memories.disable_on_external_context`: prevents threads using external context, such as MCP, web search, or tool search, from being used for memory generation
- `memories.min_rate_limit_remaining_percent`: controls the minimum remaining Codex rate-limit percentage required before memory generation starts
- `memories.extract_model`: model used for per-thread memory extraction
- `memories.consolidation_model`: model used for global memory consolidation

## Availability

According to the Codex Memories docs, Memories are not available at launch in:

- European Economic Area
- United Kingdom
- Switzerland

In those regions, rely on `AGENTS.md` and checked-in documentation for durable project guidance.

## Chronicle

Chronicle is an optional research-preview feature that can build Codex memories from recent screen context.

Chronicle is currently documented as:

- opt-in
- available only for ChatGPT Pro subscribers on macOS
- unavailable in the EEA, UK, and Switzerland
- dependent on macOS Screen Recording and Accessibility permissions

Chronicle can help Codex infer what you were looking at and what tools or workflows you used, but it has important tradeoffs:

- consumes rate limits quickly
- stores generated memories locally as unencrypted Markdown files
- can include sensitive information visible on screen
- increases prompt-injection risk from malicious screen content

## Practical Project Setup

Recommended setup for this machine:

1. Put global working preferences in:

```text
C:\Users\damador\.codex\AGENTS.md
```

2. Put project-specific rules in each repository:

```text
AGENTS.md
```

3. Keep generated memories under:

```text
C:\Users\damador\.codex\memories
```

4. Use project docs for required rules and generated Memories for helpful recall.

5. Do not store secrets in `AGENTS.md`, Memories, or Chronicle-generated files.

## Mental Model

Use this hierarchy:

1. `AGENTS.md`: durable instructions Codex must follow.
2. Repo docs: project knowledge that should be versioned.
3. Codex Memories: generated recall from previous sessions.
4. Chronicle: optional screen-context memory, only when available and acceptable for privacy/security.

