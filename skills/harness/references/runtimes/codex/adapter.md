# Codex Runtime Adapter

이 문서는 Codex에서 하네스를 구현할 때만 읽는다.

상세 실행 방식은 `references/runtimes/codex/execution-model.md`를 따른다.

## Output Paths

| 산출물 | 위치 |
|---|---|
| Pointer file | `AGENTS.md` |
| Project skill | `.codex/skills/{skill-name}/SKILL.md` |
| Agent spec | `.codex/agents/{agent-name}.md` |
| Orchestrator skill | `.codex/skills/{orchestrator-name}/SKILL.md` |
| Runtime workspace | `_workspace/` |

## Adapter Rules

- `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`, `Agent(subagent_type)`를 Codex 산출물에 요구하지 않는다.
- `.codex/agents/{name}.md`는 재사용 가능한 역할 스펙이지, 자동 등록되는 내장 subagent 타입이라고 가정하지 않는다.
- Codex 산출물은 `AGENTS.md`의 로컬 지침을 우선 적용해야 한다. 언어, 금지사항, 파일 편집 규칙이 있으면 역할 스펙과 오케스트레이터에 반영한다.
- 병렬 실행을 쓰지 않아도 같은 역할명, 입력, 출력 파일을 유지한다. 그래야 나중에 subagent 실행으로 옮기기 쉽다.
- 파일 편집 지침은 `apply_patch`와 기존 코드베이스 패턴을 우선한다.
- 보안 정보, 환경변수, API 키, 인증 정보 작업은 금지한다.

## Delegation Mapping

| Core 개념 | Codex 구현 |
|---|---|
| Specialist | `spawn_agent` worker/explorer에 역할 스펙을 요약해 전달 |
| Parallel fan-out | 여러 `spawn_agent`를 병렬로 실행, 결과는 파일/최종 메시지로 수집 |
| Reviewer | 별도 worker/explorer 또는 메인 세션의 독립 검토 단계 |
| Direct communication | 보장되지 않음. 메인 세션이 `send_input`으로 필요한 지시를 중계 |
| Shared task state | `_workspace/` ledger 또는 오케스트레이터 체크리스트 |
| Shared workspace | `_workspace/{phase}_{role}_{artifact}.md` |
| Handoff | `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md` 갱신 |

Codex에서 delegation이 불가능하거나 요청되지 않았으면, 오케스트레이터는 메인 세션에서 같은 단계를 순차 실행한다.

## Model and Effort Mapping

세션에서 모델이 상속되는 경우가 기본이다. 별도 지정이 필요하면 모델명보다 작업 난이도와 reasoning effort를 먼저 정한다.

| 복잡도 | Codex 권장 |
|---|---|
| 단순 반복/형식 검수 | 기본 모델 + low reasoning |
| 일반 분석/작성/구현 | 기본 모델 + medium reasoning |
| 복잡한 설계/추론/대규모 통합 | 상위 모델 또는 high/xhigh reasoning |

산출물에는 특정 모델명을 하드코딩하기보다 “기본 세션 모델을 상속, 필요 시 reasoning effort 상승”처럼 쓴다. 사용자가 모델을 명시했을 때만 해당 모델명을 반영한다.

## AGENTS.md Pointer Template

```markdown
## Harness: {domain}

목표: {목표 한 줄}

트리거: {도메인} 관련 다단계 작업, 하네스 실행, 하네스 수정/점검 요청 시 `{orchestrator-skill-name}` 스킬을 사용한다.

## Context 참조 규칙
1. 작업 전 `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 읽는다.
2. 상세한 Context 관리 기준은 `context/WORKING-RULES.md`를 따른다.
3. 작업에 필요한 도메인/사용자/문체 정보가 있으면 `context/` 아래 관련 파일을 찾아 읽는다.
4. 작업 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`, `BRIEF.md` 갱신 필요 여부를 확인한다.
```

AGENTS.md에는 역할 전체 목록, 긴 실행 절차, 변경 이력을 넣지 않는다. 그 내용은 `.codex/agents/`, `.codex/skills/`, `context/LESSONS.md`가 관리한다.
