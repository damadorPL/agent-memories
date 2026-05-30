# Codex Memories And Chronicle

Source docs:

- https://developers.openai.com/codex/memories
- https://developers.openai.com/codex/memories/chronicle

## Codex Memories

Codex Memories let Codex carry useful context from earlier threads into future sessions. They are meant for stable context such as:

- user preferences
- recurring workflows
- tech stacks
- project conventions
- known pitfalls

Memories are a helpful local recall layer. They should not replace `AGENTS.md`, README files, or checked-in documentation for rules that must always apply.

## Availability

Memories are off by default.

At launch, they are not available in the European Economic Area, the United Kingdom, or Switzerland.

## Enable Memories

Enable Memories in Codex settings, or add this to `~/.codex/config.toml`:

```toml
[features]
memories = true
```

On Windows, the Codex home directory is typically:

```text
C:\Users\damador\.codex
```

## How Memories Work

After Memories are enabled, Codex can turn useful context from eligible prior threads into local memory files.

Codex may skip or delay memory generation when:

- a session is still active
- a session is short-lived
- a thread has not been idle long enough
- remaining Codex rate limit is below the configured threshold

Codex updates memories in the background, not necessarily immediately when a thread ends.

## Memory Storage

Main memory files are stored under:

```text
~/.codex/memories/
```

On this Windows machine:

```text
C:\Users\damador\.codex\memories
```

The memory files may include summaries, durable entries, recent inputs, and supporting evidence from prior threads.

Treat these files as generated state. You can inspect them for troubleshooting, but do not rely on hand-editing them as the primary way to manage memory.

## Per-Thread Controls

In the Codex app and Codex TUI, use:

```text
/memories
```

This controls memory behavior for the current thread, including whether:

- existing memories can be used in the thread
- the current thread can be used to generate future memories

Thread-level choices do not change global memory settings.

## Useful Memory Settings

Common memory-related settings include:

- `memories.generate_memories`: controls whether new threads can become memory-generation inputs
- `memories.use_memories`: controls whether Codex injects existing memories into future sessions
- `memories.disable_on_external_context`: prevents threads that used external context, such as MCP, web search, or tool search, from being used for memory generation
- `memories.min_rate_limit_remaining_percent`: minimum remaining Codex rate-limit percentage required before memory generation starts
- `memories.extract_model`: model used for per-thread memory extraction
- `memories.consolidation_model`: model used for global memory consolidation

## Security Notes For Memories

Do not store secrets in memories.

Avoid storing:

- API keys
- passwords
- private tokens
- credentials
- unnecessary personal data

Codex redacts secrets from generated memory fields, but memory files should still be reviewed before sharing a Codex home directory or generated memory artifacts.

## Chronicle

Chronicle builds Codex memories from recent screen context.

It helps Codex understand what you have been working on without requiring you to restate all context in every prompt.

Chronicle can help Codex:

- use what is currently visible on screen
- fill in missing context
- remember tools and workflows
- identify the right source to inspect, such as a file, pull request, Slack thread, document, dashboard, or issue

Codex should still use the actual source directly when that source is better than screen-derived context.

## Chronicle Availability

Chronicle is an opt-in research preview.

It is currently available only for ChatGPT Pro subscribers on macOS.

It is not available in the European Economic Area, the United Kingdom, or Switzerland.

## Enable Chronicle

In the Codex app:

1. Open Settings.
2. Go to Personalization.
3. Make sure Memories are enabled.
4. Turn on Chronicle below the Memories setting.
5. Review the consent dialog.
6. Grant macOS Screen Recording and Accessibility permissions.

Chronicle requires macOS Screen Recording and Accessibility permissions.

## Pause Or Disable Chronicle

Use the Codex menu bar icon to pause or resume Chronicle.

Pause Chronicle before meetings or when viewing sensitive content that should not be used as memory context.

To disable Chronicle, turn it off in:

```text
Settings > Personalization > Memories
```

## Chronicle Storage

Temporary screen captures may appear under:

```text
$TMPDIR/chronicle/screen_recording/
```

Screen captures older than about 6 hours are deleted while Chronicle is running.

Generated Chronicle memories are stored under:

```text
$CODEX_HOME/memories_extensions/chronicle/
```

Usually:

```text
~/.codex/memories_extensions/chronicle/
```

On Windows, Chronicle is not currently available according to the docs, but the equivalent Codex home path would be:

```text
C:\Users\damador\.codex\memories_extensions\chronicle
```

> **Caveat (unverified on Windows):** The `memories_extensions/chronicle` path above is inferred from the macOS layout, not confirmed on this machine. Because Chronicle is macOS-only, no `memories_extensions/` directory exists locally. The Codex extensions that *do* exist on Windows live under `~/.codex/memories/extensions/` (e.g. an `ad_hoc/` folder) — note the different nesting (`memories/extensions/` vs. the documented `memories_extensions/`). Treat this path as a best guess until validated against an actual Chronicle-enabled install.

## Chronicle Privacy And Security

Chronicle uses screen captures, so it can include sensitive information visible on your screen.

Important notes:

- Chronicle does not access microphone or system audio.
- Do not use Chronicle to record meetings or communications without consent.
- Pause Chronicle when viewing sensitive content.
- Generated Chronicle memories are local Markdown files.
- The generated memory files are unencrypted.
- Other local programs may be able to access the temporary captures and memory files.
- Chronicle can increase prompt injection risk because malicious instructions visible on screen could influence Codex.

## What Gets Shared With OpenAI

Chronicle captures screen context locally, then uses Codex to summarize recent activity into memories.

The memory-generation session may process:

- selected screenshot frames
- OCR text extracted from screenshots
- timing information
- local file paths for the relevant time window

According to the docs, screenshots are not stored on OpenAI servers after processing unless required by law, and are not used for training.

Generated memories are stored locally on the device. When Codex uses memories in a future session, relevant memory contents may be included as context for that session.

## Practical Recommendation

Use this hierarchy for reliable project context:

1. Put required project rules in `AGENTS.md` or checked-in docs.
2. Use Codex Memories for stable preferences and recurring context.
3. Use Chronicle only when its screen-context benefits outweigh privacy, rate-limit, and prompt-injection risks.
