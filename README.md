# Agent Session Handoff Skill

Portable handoff skill and templates for continuing long coding-agent tasks in a fresh session.

This repository is source material for humans and other repositories. It does not install anything on the current machine by itself.

## 한국어 설명

이 저장소는 긴 코딩 에이전트 작업을 새 세션으로 안전하게 넘기기 위한 `new-session-handoff` 스킬과 템플릿 모음입니다.

Codex, Claude Code, Gemini 같은 코딩 에이전트로 작업하다 보면 대화가 길어지거나 context가 압축되면서 원래 목표, 변경된 요구사항, 수정한 파일, 검증 명령, 남은 작업이 흐려질 수 있습니다. 이 스킬은 이전 세션의 숨은 맥락에 기대지 않고도 다음 세션이 이어서 작업할 수 있도록 `HANDOFF.md`와 새 세션용 복붙 프롬프트를 만들게 합니다.

사용 목적:

- 긴 작업을 새 Codex/Claude/Gemini 세션으로 넘길 때
- context window가 거의 찼거나 compaction이 일어난 뒤 작업을 보존할 때
- 외부 PTY 오케스트레이터가 안전하게 세션을 회전시키기 전에 handoff artifact를 요구할 때
- 팀이나 여러 저장소에서 재사용 가능한 에이전트 작업 인계 포맷이 필요할 때

이 저장소의 원본은 `skills/new-session-handoff/`입니다. `.agents/skills/...`와 `.claude/skills/...`는 각각 Codex 호환 환경과 Claude Code 환경에서 프로젝트 스킬로 자동 발견되도록 돕는 선택적 symlink entrypoint입니다. 필수 폴더는 아니며, 사용하는 에이전트 환경의 개인/프로젝트 스킬 경로에 `skills/new-session-handoff/`를 복사하거나 symlink해서 사용할 수 있습니다.

## Contents

- `skills/new-session-handoff/`: a portable `SKILL.md` for creating self-contained handoff artifacts.
- `.agents/skills/new-session-handoff`: optional Codex-compatible project-skill entrypoint, symlinked to `skills/new-session-handoff/`.
- `.claude/skills/new-session-handoff`: optional Claude Code project-skill entrypoint, symlinked to `skills/new-session-handoff/`.
- `orchestrators/session-rotation.md`: guidance for PTY controllers such as Hermes or OpenClaw.
- `examples/`: filled handoff and resume prompt examples.

## Intended Use

`skills/new-session-handoff/` is the canonical source. Copy, vendor, or symlink it into the skill location used by your agent environment.

The `.agents/skills/...` and `.claude/skills/...` symlinks are not required for the repository to be useful. Keep them only when you want project-level automatic skill discovery in compatible tools.

Common locations include:

- Codex personal skills: `$HOME/.agents/skills/new-session-handoff/`
- Codex repo skills: `<repo>/.agents/skills/new-session-handoff/`
- Claude personal skills: `$HOME/.claude/skills/new-session-handoff/`
- Claude project skills: `<repo>/.claude/skills/new-session-handoff/`

For Claude, Gemini, or other agents, keep the same core workflow and adjust only the agent-specific installation path and session-control commands. If your tool uses a different skill discovery path, use that tool's path and keep `skills/new-session-handoff/` as the source of truth.

## Boundary

The skill prepares:

- `HANDOFF.md`
- `NEW_SESSION_PROMPT`
- readiness markers such as `SAFE_FOR_NEW_SESSION`

The skill does not execute interactive reset commands. Hermes, OpenClaw, or another PTY controller should own status checks, context thresholds, and session rotation.
