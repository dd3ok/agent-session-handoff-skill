---
name: new-session-handoff
description: Use when creating or resuming from a fresh coding-agent/Codex session handoff; includes HANDOFF.md, continuation prompt, resume prompt, 이어서 작업할 프롬프트, 핸드오프 만들어줘, 핸드오프 문서 만들어줘, 핸드오프 읽고 작업해줘, 핸드오프 문서 읽고 작업해줘, 핸드오프 보고 이어서 작업, or context-preserving summary before compaction or session rotation.
---

# New Session Handoff

## Purpose

Prepare or resume a fresh coding-agent session without relying on prior chat history, hidden reasoning, tool output, or compacted context.

This skill must not run interactive session commands such as Codex `/new`, control an agent CLI, or rotate sessions. In create mode, it writes handoff artifacts only and must not modify application code.

## Create Handoff

Use this mode when the user asks to make a handoff, says `핸드오프 만들어줘` or `핸드오프 문서 만들어줘`, save `HANDOFF.md`, prepare a new-session prompt, or preserve context before a new session.

1. Inspect current state before summarizing:
   - `pwd`
   - `git rev-parse --show-toplevel` if inside a Git repository
   - `git branch --show-current`
   - `git status --short`
   - `git diff --stat`
   - relevant instruction files: `AGENTS.md`, `AGENTS.override.md`, `PLANS.md`, `PLAN.md`, `HANDOFF.md`, `CLAUDE.md`, `GEMINI.md`

2. Read enough files to verify the state.

3. Summarize only verified facts.
   - Do not invent file paths, commands, test results, branch names, or decisions.
   - Mark unknowns as `확인 필요` or `Unknown`.
   - Prefer exact paths and exact commands.
   - Keep log snippets short and include only lines needed to identify the result or failure.

4. Produce the requested artifact(s):
   - `NEW_SESSION_PROMPT`: a copy-paste prompt for a fresh agent session.
   - `HANDOFF.md`: a self-contained Markdown handoff.

5. Write only what the user requested:
   - If asked to create a handoff, write or update `HANDOFF.md` by default unless another path was requested.
   - If asked only for a prompt, do not write files; embed a self-contained Markdown handoff draft in the prompt instead of pointing to `HANDOFF.md`.

6. Make the handoff self-contained.
   - The next session must be able to continue from the repository state and the handoff alone.
   - If the handoff conflicts with the actual working tree, instruct the next session to trust the working tree.
   - Include the smallest safe first step, not only a broad to-do list.

7. End with markers for automation:

   ```text
   HANDOFF_READY: <absolute path or not-written>
   NEW_SESSION_PROMPT_READY: yes
   VALIDATION_RECORDED: yes|no
   SAFE_FOR_NEW_SESSION: yes|no
   ```

   Use `SAFE_FOR_NEW_SESSION: yes` only when no command is still running, the handoff is complete, and the current work can be resumed from the artifacts.

## Resume From Handoff

Use this mode when the user asks to continue from a handoff, read `HANDOFF.md`, or says `핸드오프 읽고 작업해줘`, `핸드오프 문서 읽고 작업해줘`, or `핸드오프 보고 이어서 작업`.

1. Establish context before implementing:
   - Confirm `pwd`, Git root, branch, and `git status --short`.
   - Read applicable instruction files and `HANDOFF.md`.
   - Inspect files listed under "Files to inspect first".

2. Compare `HANDOFF.md` with the actual working tree.
   - If they conflict, trust the working tree and report the mismatch.
   - Mark missing or uncertain handoff details as `확인 필요` or `Unknown`.

3. Report before editing:
   - Loaded instructions
   - Repo state
   - Handoff consistency
   - Smallest next step

4. Continue from the smallest remaining task, following repository instructions and running the narrowest relevant validation after changes.

## Templates

Read the matching one-level reference before drafting each artifact:

- Read `references/handoff-template.md` when producing or updating `HANDOFF.md`.
- Read `references/new-session-prompt-template.txt` when producing `NEW_SESSION_PROMPT`.

## Automation Boundary

External agents such as Hermes or OpenClaw may use the final markers to decide when to send a session-reset command to an agent CLI PTY. This skill itself must not claim to execute `/new` or any other interactive reset command; it only prepares safe continuation artifacts.
