# Claude Code Orchestrator Overlay

`references/templates/core/orchestrator.md`와 함께 사용한다.

## 실행 모드

| Core 필요 | Claude Code 동작 |
|---|---|
| 직접 팀 조율 | 가능하면 Agent Teams를 사용한다 |
| 병렬 specialist | `TeamCreate` + 작업 할당을 사용하고, 불가능하면 background `Agent`로 대체한다 |
| 역할 프롬프트 | 재사용 역할은 `.claude/agents/{role}.md`를 사용한다 |
| 공유 업데이트 | 가능하면 `TaskCreate`/`TaskUpdate`를 사용한다 |
| 역할 간 직접 통신 | 다른 역할의 작업을 바꾸는 정보에만 `SendMessage`를 사용한다 |

## Team Mode 기본 구조

```text
1. 선택한 member로 team을 만든다.
2. owner와 dependency가 있는 task를 등록한다.
3. member는 지속 산출물을 `_workspace/`에 쓴다.
4. leader는 진행 상황을 확인한다.
5. leader는 산출물을 읽고 통합한다.
6. 완료 후 team을 삭제하거나 닫는다.
```

Agent Teams를 사용할 수 있으면 생성된 Claude Code orchestrator는 다음을 포함할 수 있다:

```text
TeamCreate(team_name: "{domain}-team", members: [...])
TaskCreate(tasks: [{ title, description, assignee, depends_on }])
SendMessage({to: "{member}", message: "{information that changes their work}"})
TaskUpdate(...)
TeamDelete(...)
```

`SendMessage({to: "all"})`은 모든 member의 작업을 바꾸는 정보에만 사용한다.

## Role Prompt 요구사항

agent나 team member를 실행할 때는 다음을 포함한다:

1. 역할 이름과 책임 범위
2. 읽어야 할 파일
3. `_workspace/` 아래 출력 경로
4. 다른 member에게 공유해야 하는 정보의 기준
5. 지속되어야 하는 발견은 파일에 남기라는 지시
6. 실패하거나 일부만 끝났을 때 미완료 범위를 보고하라는 지시

## Agent 대체 실행 기본 구조

```text
1. 선택한 agent를 직접 호출한다.
2. 독립 작업에만 background 실행을 사용한다.
3. 반환 요약과 `_workspace/` 파일을 수집한다.
4. 메인 세션에서 통합한다.
```

subagent mode를 사용할 때 생성된 Claude Code orchestrator는 다음을 명시한다:

- `subagent_type`
- 역할 프롬프트
- 입력 파일
- `_workspace/` 아래 출력 경로
- 독립 작업일 때 `run_in_background`
- runtime policy가 요구하는 경우 model

## Hybrid Mode

Hybrid orchestrator는 Phase mode 표를 포함해야 한다:

| Phase | Mode | 이유 |
|---|---|---|
| Phase N | Team mode 또는 subagent mode | 이 mode를 쓰는 이유 |

전환 규칙:

- Team -> subagent: 환경이 활성 team 1개만 허용하면 subagent 호출 전에 team을 삭제하거나 닫는다.
- Subagent -> team: subagent 파일 산출물을 team member의 read path로 넘긴다.
- Team -> team: 산출물을 `_workspace/`에 보존하고 이전 team을 삭제하거나 닫은 뒤 다음 team을 만든다.

## State Check

Claude Code orchestrator는 Phase 0에 다음 형태를 포함한다:

1. `CLAUDE.md`, `.claude/skills/`, `.claude/agents/`, `context/`, `BRIEF.md`, `HANDOFF.md`, `ROADMAP.md`를 감지한다.
2. `_workspace/`를 감지한다.
3. 실행 모드를 고른다.
   - `_workspace/` 없음: 초기 실행
   - `_workspace/` 있음 + 특정 피드백: 부분 재실행
   - `_workspace/` 있음 + 새 입력: 이전 산출물을 보존한 뒤 새 실행
4. 부분 재실행이면 영향받은 역할, 입력 파일, 출력 파일, 검증 범위를 명시한다.
5. 새 실행이면 이전 workspace 산출물을 어디에 보존했는지 기록한다.

기존 workspace를 보존할 때는 `_workspace_YYYYMMDD_HHMMSS/` 같은 timestamp archive 경로나 프로젝트 표준 경로를 사용한다. 새로 시작한다는 이유만으로 이전 산출물을 삭제하지 않는다.

## Claude Code 전용 검증

- Team mode 지시는 `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`를 언급할 수 있다.
- Subagent mode 지시는 `Agent`, `subagent_type`, `run_in_background`, model name을 언급할 수 있다.
- team message를 쓰더라도 지속되어야 하는 발견은 `_workspace/`에 남긴다.
- 세션 갱신 Phase는 Claude Code에서 사용 가능한 구체 파일 edit/read 도구를 언급할 수 있다.
- Codex 전용 `spawn_agent`, `wait_agent`, `close_agent`를 Claude Code 필수 실행 조건으로 쓰지 않는다.
- 부분 재실행이 영향받은 역할, 원본 산출물, 대체 산출물, 검증 범위를 명시하는지 확인한다.
