# Codex Orchestrator Overlay

`references/templates/core/orchestrator.md`와 함께 사용한다.

## 실행 모드

| Core 필요 | Codex 동작 |
|---|---|
| 순차 실행 | 메인 세션이 Phase를 직접 실행한다 |
| 병렬 specialist | 사용자가 subagent/delegation/병렬 에이전트 작업을 명시적으로 요청한 경우에만 `spawn_agent`를 사용한다 |
| 역할 프롬프트 | `.codex/agents/{role}.md`를 붙여넣거나 요약한다. 자동 등록된 타입이라고 가정하지 않는다 |
| 대기 | `wait_agent`는 다음 단계가 결과에 막힐 때만 제한적으로 사용한다 |
| 후속 지시 | 기존 spawned agent가 아직 관련 있을 때만 `send_input`을 사용한다 |
| 팀 통신 | 보장되지 않는다. 메인 세션이 파일 또는 `send_input`으로 결정을 중계한다 |
| 작업 추적 | `_workspace/task-ledger.md` 또는 오케스트레이터 체크리스트를 사용한다 |

## Role Prompt 요구사항

worker를 실행할 때는 다음을 포함한다:

1. 같은 코드베이스에서 혼자 작업하는 것이 아니라는 점
2. 소유 파일 또는 책임 범위
3. 읽어야 할 파일
4. `_workspace/` 아래 출력 경로
5. 관련 없는 변경을 되돌리지 말라는 지시
6. 파일을 수정했다면 최종 응답에 변경 파일 목록을 적으라는 지시

## 메인 세션 대체 실행

delegation이 불가능하거나 요청되지 않았으면 다음처럼 실행한다:

1. 역할 순서를 메인 세션에서 실행한다.
2. 각 역할의 산출물을 별도 `_workspace/` 파일로 남긴다.
3. 나중에 subagent 실행으로 옮기기 쉽도록 제목에 역할명을 유지한다.
4. 병렬 설계와 같은 검증 gate를 유지한다.
5. 메인 세션이 대신 수행한 역할은 표시한다.
6. spawned worker가 사용했을 입력/출력 계약을 그대로 유지한다.

## Hybrid 실행

Codex orchestrator는 delegation, subagent, 병렬 에이전트 작업이 명시적으로 요청됐고 사용 가능한 경우에만 spawned agent를 사용한다. 그 외의 hybrid 실행은 메인 세션 안에서 논리적 역할 패턴만 바꾸는 것을 뜻한다.

Phase 사이에서 실행 형태가 바뀌면 다음을 따른다:

1. Phase 산출물을 `_workspace/` 아래에 저장한다.
2. 다음 Phase 입력을 명시적으로 요약한다.
3. 더 필요 없는 spawned agent를 닫는다.
4. 추가 delegation이 허용되지 않으면 메인 세션에서 계속한다.

## State Check

Codex orchestrator는 Phase 0에 다음 형태를 포함한다:

1. `AGENTS.md`, `.codex/skills/`, `.codex/agents/`, `context/`, `BRIEF.md`, `HANDOFF.md`, `ROADMAP.md`를 감지한다.
2. `_workspace/`를 감지한다.
3. 실행 모드를 고른다.
   - `_workspace/` 없음: 초기 실행
   - `_workspace/` 있음 + 특정 피드백: 부분 재실행
   - `_workspace/` 있음 + 새 입력: 이전 산출물을 보존한 뒤 새 실행
4. 부분 재실행이면 영향받은 역할, 입력 파일, 출력 파일, 검증 범위를 명시한다.
5. 새 실행이면 이전 workspace 산출물을 어디에 보존했는지 기록한다.

Codex 산출물에 team deletion 같은 Claude Code cleanup 단계를 하드코딩하지 않는다.

기존 workspace를 보존할 때는 `_workspace_YYYYMMDD_HHMMSS/` 같은 timestamp archive 경로나 프로젝트 표준 경로를 사용한다. 새로 시작한다는 이유만으로 이전 산출물을 삭제하지 않는다.

## Codex 전용 검증

- Claude Code 도구를 요구하는 지시를 만들지 않는다.
- 중첩 delegation이 가능하다고 가정하지 않는다.
- 수동 편집은 `apply_patch`를 우선한다.
- `AGENTS.md`와 로컬 workspace 지침을 따른다.
- 세션 갱신 Phase는 로컬 파일 편집 규칙을 지키고 변경 파일을 확인해야 한다.
- `.codex/agents/{role}.md`가 자동 등록된 runtime type이 아니라 역할 스펙으로 참조되는지 확인한다.
- spawned-worker 프롬프트에 소유 범위, 읽을 입력, 출력 경로, 관련 없는 변경 금지 안내가 포함됐는지 확인한다.
- delegation이 요청되지 않았을 때 fallback 실행이 완결되는지 확인한다.
- 생성된 프롬프트가 worker에게 환경변수, API 키, 토큰, 인증 정보를 다루라고 요구하지 않는지 확인한다.
- 부분 재실행이 영향받은 역할, 원본 산출물, 대체 산출물, 검증 범위를 명시하는지 확인한다.
