# Claude Code Execution Model

이 문서는 Claude Code 전용 실행 모델을 정의한다. Claude Code에서는 같은 core delegation 개념이 Agent Teams와 Subagent Mode로 구현된다.

## Team Mode

팀 리더가 `TeamCreate`로 팀을 구성하고 팀원은 독립 Claude Code 인스턴스로 실행된다. 팀원은 `SendMessage`로 직접 메시지를 주고받고 `TaskCreate`/`TaskUpdate` 기반 공유 작업 목록으로 조율할 수 있다.

```text
[리더] <-> [팀원A] <-> [팀원B]
  ^          ^          ^
  +------ 공유 작업 목록 ------+
```

핵심 도구:

- `TeamCreate`: 팀 생성과 팀원 스폰
- `SendMessage({to: name})`: 특정 팀원에게 메시지
- `SendMessage({to: "all"})`: 브로드캐스트. 비용이 높으므로 드물게 사용
- `TaskCreate` / `TaskUpdate`: 공유 작업 목록 생성과 상태 갱신

특징:

- 팀원 간 직접 대화, 도전, 검증 가능
- 리더를 거치지 않는 팀원 간 정보 교환 가능
- 공유 작업 목록으로 자체 조율
- 팀원이 자체 작업을 요청하거나 진행 상태를 갱신 가능
- 팀원이 유휴 상태가 되면 리더에게 알림
- 계획 승인 모드로 위험한 작업 전 검토 가능
- Phase 사이에 팀을 정리하고 새 팀을 구성할 수 있음

제약:

- 세션당 한 팀만 활성화 가능
- 중첩 팀 불가
- 리더 이전 불가
- 토큰 비용이 높음

팀 재구성:

Phase별로 다른 전문가 조합이 필요하면 이전 팀의 산출물을 `_workspace/`에 저장한 뒤 팀을 정리하고 새 팀을 구성한다. 새 팀은 `_workspace/`에 남은 산출물과 context 파일을 읽고 이어받는다.

## Subagent Mode

메인 에이전트가 `Agent(prompt, subagent_type, run_in_background)`로 단발성 에이전트를 호출하고 결과를 메인에게만 반환받는다.

```text
[메인] -> [서브A] -> 결과 반환
       -> [서브B] -> 결과 반환
       -> [서브C] -> 결과 반환
```

특징:

- 가볍고 빠름
- 결과 수집이 단순
- 결과가 메인 컨텍스트로 요약 반환됨
- 토큰 효율적
- 에이전트 간 직접 통신 없음

제약:

- 서브 에이전트끼리 직접 통신할 수 없음
- 메인 에이전트가 모든 조율을 맡음
- 실시간 협업, 도전, 상호 검증이 제한됨

## Mode Decision

```text
에이전트가 2개 이상인가?
├─ 아니오: Subagent Mode 또는 단일 실행
└─ 예: 에이전트 간 직접 통신이 필요한가?
   ├─ 예: Team Mode
   └─ 아니오: Subagent Mode도 가능
```

기본값은 Team Mode다. 단, 결과 전달만 필요한 전문가 풀, 단순 생성-검증, 단발성 조사라면 Subagent Mode를 선택할 수 있다.

## Core Pattern Mapping

| Core pattern | Claude Code 구현 |
|---|---|
| Pipeline | 순차 `Agent` 호출 또는 단일 orchestrator pass |
| Fan-out/Fan-in | 가능하면 `TeamCreate` + 작업 할당 + 팀 메시지 |
| Expert Pool | router가 선택한 `Agent`만 호출 |
| Producer-Reviewer | team pair 또는 제한된 `Agent` loop |
| Supervisor | 가능하면 `TaskCreate`/`TaskUpdate`, 아니면 supervisor file ledger |
| Hierarchical Delegation | 가능하면 평탄화하고 깊은 중첩 team은 피한다 |

패턴별 적용 메모:

- Fan-out/Fan-in: 다른 팀원의 작업에 영향을 주는 발견은 `SendMessage`로 공유하고, 지속 산출물은 파일에 남긴다.
- Expert Pool: 병렬 실행이 유용하면 선택된 expert에 `Agent(..., run_in_background=true)`를 사용한다.
- Producer-Reviewer: producer/reviewer가 실시간 확인을 주고받아야 하면 team pair를 쓰고, 아니면 제한된 `Agent` loop를 쓴다.
- Supervisor: 가능하면 `TaskCreate`/`TaskUpdate`로 claim/progress를 관리하고, 아니면 file ledger를 유지한다.
- Hierarchical Delegation: L0/L1은 하나의 team으로 두고 L2는 `Agent` 호출로 처리하거나, 단일 team으로 평탄화한다.

## Claude Built-in Types

| 타입 | 도구 접근 | 적합한 용도 |
|---|---|---|
| `general-purpose` | 전체 도구. WebSearch, WebFetch 포함 | 웹 조사, 범용 작업, 검증 실행 |
| `Explore` | 읽기 전용. Edit/Write 없음 | 코드베이스 탐색, 분석 |
| `Plan` | 읽기 전용. Edit/Write 없음 | 아키텍처 설계, 계획 수립 |

커스텀 에이전트는 `.claude/agents/{name}.md`에 정의하고 `subagent_type: "{name}"`으로 호출한다. 커스텀 에이전트는 전체 도구에 접근할 수 있다.

선택 기준:

| 상황 | 권장 |
|---|---|
| 역할이 복잡하고 여러 세션에서 재사용됨 | 커스텀 에이전트 정의 |
| 단순 조사/수집이고 프롬프트만으로 충분함 | `general-purpose` + 상세 프롬프트 |
| 코드 읽기만 필요함 | `Explore` |
| 설계/계획만 필요함 | `Plan` |
| 파일 수정이 필요한 구현 작업 | 커스텀 에이전트 정의 |

재사용 역할은 빌트인 타입을 쓰더라도 `.claude/agents/{name}.md`에 역할 정의를 남긴다. 파일로 존재해야 다음 세션에서 재사용할 수 있고, 팀 통신 프로토콜을 명시할 수 있다.

## Model Mapping

| 모델 | 사용 기준 | 예시 역할 |
|---|---|---|
| `haiku` | 정해진 규칙대로 수행하는 단순 업무, 오타/형식 검수 | 스타일 가이드 검수, 단순 포맷팅 |
| `sonnet` | 대부분의 일반 분석, 작성, 구현. 기본값 | 코드 리뷰, 문서 작성, 데이터 추출 |
| `opus` | 복잡한 설계, 추론, 고품질 창작 | 시스템 설계, 복잡한 디버깅, 창작 기획 |

Agent 도구 호출 시 역할과 업무 복잡도에 따라 `model` 파라미터를 명시한다. 기본값은 `sonnet`이다. `sonnet`으로 품질이 부족하면 `opus`로 재시도할 수 있다.

## Agent Definition Skeleton

```markdown
---
name: agent-name
description: "1-2문장 역할 설명. 트리거 키워드를 포함."
---

# Agent Name — 역할 한 줄 요약

당신은 [도메인]의 [역할] 전문가입니다.

## 핵심 역할
1.

## 작업 원칙
-

## 입력/출력 프로토콜
- 입력:
- 출력:
- 형식:

## Context 로딩
1. `context/` 아래 Markdown 파일을 확인한다.
2. BRAND-VOICE, WORKING-RULES, GLOSSARY, ABOUT-ME를 우선 참조한다.
3. 작업 중 context 규칙과 충돌하면 보고하고 수정한다.

## 팀 통신 프로토콜
- 메시지 수신:
- 메시지 발신:
- 작업 요청:

## 에러 핸들링
- 실패 시:
- 타임아웃 시:

## 협업
- 다른 에이전트와의 관계:
```
