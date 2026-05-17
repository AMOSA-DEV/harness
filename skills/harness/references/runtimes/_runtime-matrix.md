# Runtime Matrix

런타임은 “AI 제공사”가 아니라 “하네스가 실제로 실행되는 에이전트 환경”이다.

| 항목 | Codex | Claude Code |
|---|---|---|
| Adapter 문서 | `runtimes/codex/adapter.md` | `runtimes/claude-code/adapter.md` |
| 실행 모델 문서 | `runtimes/codex/execution-model.md` | `runtimes/claude-code/execution-model.md` |
| Orchestrator template overlay | `templates/runtimes/codex/orchestrator-overlay.md` | `templates/runtimes/claude-code/orchestrator-overlay.md` |
| Pointer file | `AGENTS.md` | `CLAUDE.md` |
| Project skills | `.codex/skills/{name}/SKILL.md` | `.claude/skills/{name}/SKILL.md` |
| Global skills | `~/.codex/skills/{name}/SKILL.md` | `~/.claude/skills/{name}/SKILL.md` |
| Agent specs | `.codex/agents/{name}.md` | `.claude/agents/{name}.md` |
| 런타임 용어 | Delegation, worker/explorer, spawned agent | Agent Teams, Team member, Subagent |
| Delegation | 사용 가능하고 허용된 경우 `spawn_agent`, `send_input`, `wait_agent`, `close_agent` | 사용 가능한 경우 `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`, `Agent` |
| 직접 팀 통신 | 보장되지 않음 | Agent Teams 환경에서 지원 |
| 공유 작업 상태 | file ledger 또는 orchestrator checklist | 사용 가능한 경우 `TaskCreate`/`TaskUpdate` |
| 기본 교환 방식 | 파일 기반 `_workspace/machine/` + 반환 요약 | 파일 기반 `_workspace/machine/` + 팀 메시지/task |
| 모델 이름 | GPT/Codex 모델명. 대개 세션에서 상속 | `haiku`, `sonnet`, `opus` |
| effort 제어 | 사용 가능한 경우 reasoning effort | model tier 선택 |
| 수동 편집 | `apply_patch` 우선 | Claude Code edit/write 도구 |
| 패키징 | plugin 패키징 시 `.codex-plugin/plugin.json` | plugin 패키징 시 `.claude-plugin/plugin.json` |

## 감지

사용자 의도를 먼저 따른다. 사용자가 “Codex용”이라고 하면 Codex를 고르고, “Claude Code용”이라고 하면 Claude Code를 고른다.

사용자 의도가 없으면 현재 환경에서 추정한다:

| 신호 | Runtime |
|---|---|
| `AGENTS.md`, `.codex/`, `~/.codex/skills`, Codex tools | Codex |
| `CLAUDE.md`, `.claude/`, `~/.claude/skills`, Claude Code tools | Claude Code |

둘 다 있으면 대상이 모호하고 잘못된 파일 생성 비용이 클 때만 묻는다. 아니면 활성 runtime을 먼저 생성하고, 다른 runtime을 나중에 추가하는 방법을 남긴다.

## 재분류 규칙

Claude Code 자료를 Codex까지 지원하는 구조로 옮길 때는 다음을 따른다:

1. 각 지시를 core, runtime-specific, context-protocol, template, example, obsolete로 다시 분류한다.
2. core team logic과 파일 기반 `_workspace/audit/`, `_workspace/machine/`, `_workspace/human/` 교환은 보존한다.
3. Claude Code team primitive는 명시적으로 허용된 경우에만 Codex delegation으로 바꾼다.
4. orchestrator가 메인 세션에서도 실행될 수 있도록 fallback 경로를 보존한다.
5. `.claude`를 `.codex`로 단순 치환하지 않는다. 역할 semantics와 도구 가용성이 다르다.

## 용어 정규화

문서 구조에서는 두 런타임 모두 `adapter.md`와 `execution-model.md`를 사용한다.

| 공통 개념 | Codex 용어 | Claude Code 용어 |
|---|---|---|
| 런타임 산출물 경로와 제약 | Runtime Adapter | Runtime Adapter |
| 역할 실행 방식 | Delegation | Agent Teams / Subagent |
| 병렬 역할 | spawned worker/explorer | Team member 또는 background `Agent` |
| 역할 간 중계 | 메인 세션의 `send_input` 또는 파일 기록 | `SendMessage` 또는 공유 task |
| 공유 상태 | `_workspace/machine/` ledger | `TaskCreate`/`TaskUpdate`와 `_workspace/machine/` |
