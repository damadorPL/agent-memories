# How Codex Remembers Project Conversations

Codex does not automatically remember every conversation forever. For project work, the reliable way to preserve context is to write the important details into files inside the project or into a configured memory location.

## Recommended Project Memory File

Keep project-specific context in a Markdown file such as:

```text
CODEX_PROJECT_MEMORY.md
```

Use it for information Codex should reuse in future sessions:

- project goals and current priorities
- architecture decisions
- coding conventions
- test commands
- setup instructions
- known issues
- user preferences for this project
- links to important files or docs

## What To Save

Good memory entries are short, concrete, and actionable.

Example:

```markdown
## Testing

Run `npm test` before commits. For frontend changes, also run `npm run build`.

## Style

Use existing React component patterns. Avoid adding new UI libraries unless explicitly requested.

## Current Focus

The project is building long-term agent memory for Codex across coding sessions.
```

## How Codex Uses It

At the start of a future task, Codex can read this file to recover project context. The file acts like durable memory because it is stored in the repository instead of only in the chat.

## What Not To Store

Do not store secrets or credentials in project memory files.

Avoid saving:

- API keys
- passwords
- private tokens
- personal data that is not needed for development

Use environment variables, secret managers, or ignored local files for sensitive values.

## Suggested Workflow

1. When an important decision is made, add a short note to this file.
2. When setup or test commands change, update the note.
3. When a task is completed, summarize only durable information that will matter later.
4. Remove outdated notes so future sessions do not follow stale context.

## Example Memory Entry

```markdown
## 2026-05-25

- Project goal: experiment with persistent memory for coding agents.
- Workspace: `C:\Users\damador\Documents\Code\agent-memory`.
- Preference: keep notes concise and project-specific.
- Safety: never store credentials in memory files.
```
