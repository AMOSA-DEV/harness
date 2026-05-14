# Codex Execution Model

이 문서는 Codex 전용 실행 모델을 정의한다. Codex에서는 같은 core delegation 개념이 메인 세션 조율, worker/explorer, spawned agent로 구현된다.

## 기본 원칙

Codex에서 `.codex/agents/{name}.md`는 재사용 가능한 역할 스펙이다. 이 파일이 곧바로 `spawn_agent`의 `agent_type`으로 등록된다고 가정하지 않는다.

Delegation은 다음 조건을 모두 만족할 때만 사용한다:

1. 사용자가 subagent, delegation, 병렬 에이전트 작업을 명시적으로 요청했다.
2. 현재 세션에서 `spawn_agent` 계열 도구가 사용 가능하다.
3. 작업이 역할별로 독립되어 있고, 소유 파일이나 책임 범위를 분리할 수 있다.
4. 메인 세션이 기다리는 동안 수행할 수 있는 비중복 작업이 있다.

조건을 만족하지 않으면 메인 세션에서 같은 역할 순서를 순차 실행한다. 이때도 역할명, 입력, 출력 파일은 유지한다.

## 실행 형태

```text
[메인 세션]
  ├─ spawn_agent(worker/explorer) -> 역할 A 산출물
  ├─ spawn_agent(worker/explorer) -> 역할 B 산출물
  ├─ 필요할 때만 wait_agent
  ├─ send_input으로 필요한 지시 중계
  └─ 결과 통합 후 close_agent
```

직접 team channel이나 공유 task primitive가 보장되지 않으므로, 지속 상태는 `_workspace/` 파일과 오케스트레이터 체크리스트에 둔다.

## Agent Type 선택

Codex의 실제 `agent_type`은 현재 런타임이 제공하는 값만 사용한다. 역할 이름 파일을 그대로 넣지 않는다.

| 상황 | 권장 |
|---|---|
| 코드베이스 탐색, 읽기 중심 분석 | 사용 가능한 explorer/read-only 타입 |
| 파일 수정, 구현, 문서 패치 | 사용 가능한 worker 타입 |
| 단순 순차 작업, delegation 미요청 | 메인 세션 직접 실행 |
| 역할 스펙이 필요함 | `.codex/agents/{name}.md` 내용을 프롬프트에 요약 |

## Core Pattern Mapping

| Core pattern | Codex 구현 |
|---|---|
| Pipeline | 메인 세션 또는 worker 1개씩 순차 실행. 이전 파일 산출물을 다음 프롬프트 입력으로 넘긴다 |
| Fan-out/Fan-in | 허용된 경우 여러 `spawn_agent` worker를 병렬 실행한다. 아니면 격리된 순차 pass로 실행한다 |
| Expert Pool | 메인 세션이 라우팅하고, 허용된 경우 선택된 worker만 실행한다 |
| Producer-Reviewer | producer worker + reviewer worker, 또는 메인 세션의 2-pass 루프 |
| Supervisor | 메인 세션이 supervisor 역할을 맡고, worker는 겹치지 않는 batch를 소유한다 |
| Hierarchical Delegation | 평탄화를 우선한다. 명시적으로 지원되지 않으면 중첩 agent tree를 피한다 |

## Worker Prompt 필수 항목

worker에게 넘기는 프롬프트에는 다음을 포함한다:

- 맡은 역할과 역할 스펙 요약
- 읽을 파일 목록
- 소유 파일 또는 책임 범위
- `_workspace/` 아래 출력 경로
- 같은 코드베이스에서 다른 작업자가 있을 수 있다는 안내
- 관련 없는 변경을 되돌리지 말라는 지시
- 파일을 수정했다면 최종 응답에 변경 파일 목록을 적으라는 지시

## Agent Spec Template

```markdown
---
name: {agent-name}
description: "{역할과 트리거를 1-2문장으로 설명}"
---

# {Agent Name}

## 역할
{핵심 책임}

## 입력
{읽을 파일, 사용자 입력, 이전 단계 산출물}

## 출력
{쓸 파일과 형식}

## 작업 원칙
- {원칙}

## Context 로딩
- 작업 전 `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 읽는다.
- 필요한 경우 `context/ABOUT-ME.md`, `context/BRAND-VOICE.md`, `context/GLOSSARY.md`, `context/LESSONS.md`를 읽는다.
- `context/` 아래 추가 Markdown 파일이 있으면 작업과 관련된 파일만 읽고, 없는 표준 파일은 누락으로 표시한다.
- 이전 산출물이 있으면 새로 만들기 전에 읽고, 사용자 피드백을 반영해 개선한다.

## 협업 및 경계
- 이 역할이 소유한 파일/책임 범위: {scope}
- 다른 역할의 산출물이 필요한 경우 `_workspace/` 경로를 입력으로 받는다.
- 상충되는 판단은 삭제하지 말고 근거와 함께 보고한다.

## 실패 처리
- 입력이 부족하면 필요한 파일이나 질문을 명시한다.
- 실패한 범위와 미검증 범위를 출력에 표시한다.

## Codex 실행 지침
- 이 파일은 역할 스펙이다. subagent로 실행할 때는 이 내용을 프롬프트에 요약해 전달한다.
- 병렬 실행이 허용되지 않으면 메인 세션에서 순차 실행한다.
- 변경 전 관련 파일을 읽고, 수동 편집은 apply_patch를 우선한다.
- worker로 실행할 경우 다른 작업자가 있을 수 있음을 전제하고, 관련 없는 변경을 되돌리지 않는다.
```

## Worker Prompt Template

```markdown
당신은 {agent-name} 역할을 맡는다. 아래 역할 스펙을 따른다: {role-spec-summary}

읽을 파일:
- {input-files}

소유 범위:
- {owned-files-or-responsibility}

출력:
- `_workspace/{phase}_{agent}_{artifact}.md`

주의:
- 당신은 혼자 작업하는 것이 아니다. 다른 작업자가 같은 코드베이스에서 작업할 수 있다.
- 관련 없는 변경을 되돌리지 않는다.
- 편집 전 관련 파일을 읽고 기존 패턴을 따른다.
- 파일을 수정했다면 최종 응답에 변경 파일 목록을 적는다.
- 작업이 실패하거나 일부만 끝났으면 미완료 범위와 검증하지 못한 범위를 적는다.
```

## 대기와 통합

`wait_agent`는 다음 단계가 결과에 막힐 때만 사용한다. 기다리는 동안 메인 세션은 겹치지 않는 준비, 검증, 문서 읽기를 수행한다.

결과가 도착하면 메인 세션이 다음을 수행한다:

1. worker 최종 응답과 `_workspace/` 산출물을 읽는다.
2. 누락, 충돌, 미검증 범위를 표시한다.
3. 필요한 경우 영향받은 역할만 다시 실행한다.
4. 더 필요 없는 spawned agent는 `close_agent`로 닫는다.

## Fallback

delegation이 불가능하거나 요청되지 않았으면 다음을 지킨다:

1. 메인 세션이 역할을 순서대로 수행한다.
2. 각 역할 산출물을 별도 `_workspace/` 파일에 남긴다.
3. 산출물 제목에 역할명을 유지한다.
4. 병렬 설계와 동일한 검증 기준을 적용한다.
5. 메인 세션이 대체 수행한 역할은 최종 보고에 표시한다.

## 금지

- Claude Code 도구명(`TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`, `Agent(subagent_type)`)을 Codex 산출물의 필수 실행 조건으로 쓰지 않는다.
- `.codex/agents/{name}.md`를 자동 등록된 subagent type으로 가정하지 않는다.
- worker에게 환경변수, API 키, 토큰, 인증 정보 처리를 맡기지 않는다.
- 같은 파일을 여러 worker가 동시에 수정하도록 설계하지 않는다.
