# Eval: Resume Cleanup

## Scenario

The user asks: "핸드오프 읽고 이어서 해줘." The handoff is safe, consistent with disk state, and was generated at `.new-session-handoff/HANDOFF.md`.

## Expected

- Verifies cwd, Git root, branch, short HEAD, `git status --short`, and `git diff --stat`.
- Reads applicable instruction files and the handoff.
- Reports handoff consistency before editing.
- Deletes generated handoff artifacts only after verification.
- Deletes only untracked artifacts.
- Preserves tracked, stale, unsafe, or user-requested audit handoff files.

## Failure Conditions

- Deletes the handoff before disk verification.
- Deletes a tracked handoff file.
- Deletes an unsafe or stale handoff.
- Continues implementation without reporting a mismatch.
