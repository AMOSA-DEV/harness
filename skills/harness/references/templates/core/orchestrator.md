# Orchestrator Template

이 문서는 런타임 중립 오케스트레이터 템플릿이다. 구체 실행 도구는 `references/templates/runtimes/{runtime}/orchestrator-overlay.md`를 함께 읽어 결정한다.

## Purpose

오케스트레이터는 역할, 스킬, 산출물 경로, 검증, 후처리를 하나의 실행 흐름으로 묶는다.

## Required Metadata

```markdown
---
name: {domain}-orchestrator
description: "{domain} 하네스를 실행하고 수정/보완/재실행하는 오케스트레이터. 초기 실행, 부분 재실행, 업데이트, 이전 결과 개선 요청 시 사용."
---
```

description에는 초기 실행뿐 아니라 후속 작업 키워드를 반드시 넣는다.

## Sections

### 1. Goal

- 최종 산출물
- 성공 기준
- 비목표

### 2. Runtime

- 선택한 runtime
- 적용한 core team architecture pattern
- 적용한 runtime overlay
- 실행 형태: 단일 오케스트레이터, 격리된 역할 호출, 협업 팀, 또는 Phase별 혼합
- delegation 가능 여부와 fallback 방식

하이브리드 실행이면 Phase별 실행 형태와 이유를 표로 남긴다:

| Phase | 실행 형태 | 이유 | 입력 | 출력 |
|---|---|---|---|---|
| Phase N | {execution-shape} | {reason} | {input} | {output} |

### 3. Role Map

| Role | Responsibility | Skill | Input | Output |
|---|---|---|---|---|
| {role} | {responsibility} | {skill-name} | {input} | `_workspace/machine/{phase}_{role}_{artifact}.md` |

역할 간 직접 협업이 필요하면 다음 항목을 추가한다:

| From | To | When | Information |
|---|---|---|---|
| {role-a} | {role-b} | {condition} | {information that changes the receiver's work} |

### 4. Phase 0: State Check

1. pointer file 존재 여부 확인
2. context/세션 파일 존재 여부 확인
3. `_workspace/` 존재 여부와 목적별 폴더 확인
   - `_workspace/audit/`: 감사, QA, 구조 검증 결과
   - `_workspace/machine/`: 재작업과 부분 재실행에 필요한 역할별 산출물
   - `_workspace/human/`: HUMAN 검토용 최종 산출물
4. 실행 유형 결정:
   - `_workspace/` 없음: 초기 실행
   - `_workspace/` 있음 + 부분 수정 요청: 부분 재실행
   - `_workspace/` 있음 + 새 입력: 이전 workspace를 timestamp 폴더로 보존 후 새 실행
5. 부분 재실행이면 수정 대상 역할, 입력 파일, 재검증 범위를 명시한다.
6. 새 실행이면 `_workspace/` 전체를 `_workspace_archive/YYYYMMDD_HHMMSS/` 또는 프로젝트 표준 timestamp 백업 경로에 보존하고, 백업 위치를 기록한다.
7. 기존 역할/스킬이 있으면 오케스트레이터가 참조하는 이름과 실제 파일명을 대조한다.
8. 런타임 도구가 사용 가능한지 확인하고, 불가능한 도구는 같은 산출물을 만드는 fallback으로 바꾼다.

### 5. Phase 1: Preparation

1. 사용자 입력과 목표 확인
2. `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 먼저 읽기
3. 작업에 필요한 추가 context 파일 읽기
4. 사용자 숙련도와 설명 수준을 추정할 수 있는 근거가 있으면 작업 패킷의 커뮤니케이션 기준에 반영한다.
5. `_workspace/machine/00_input/`에 입력 스냅샷 저장
6. 역할별 작업 패킷 작성
7. Phase 간 의존성을 정리한다.

작업 패킷에는 다음 항목을 포함한다:

| Item | Meaning |
|---|---|
| Goal | 해당 역할의 목표 |
| Input | 읽을 파일과 이전 산출물 |
| Output | 쓸 파일과 형식 |
| Skill | 사용할 스킬 또는 인라인 절차 |
| Validation | 역할별 통과 기준 |
| Failure | 실패 시 행동 |

### 6. Phase 2: Role Execution

선택한 team architecture pattern에 따라 역할을 실행한다.

필수 규칙:

- 모든 역할은 입력과 출력 경로가 명확해야 한다.
- 역할별 소유 범위와 비소유 범위를 명시한다.
- 큰 산출물은 반환값보다 파일에 저장한다.
- 실패한 역할이 있으면 최종 산출물에 누락을 표시한다.
- 런타임이 병렬 실행을 지원하지 않으면 순차 실행한다.
- 병렬 또는 협업 실행을 하더라도 durable output은 목적에 맞게 `_workspace/audit/`, `_workspace/machine/`, `_workspace/human/`에 남긴다.
- Phase별 실행 형태가 바뀌면 이전 Phase의 파일 산출물을 다음 Phase 입력으로 넘긴다.
- 역할이 이전 산출물을 수정하는 경우 새로 만들기 전에 기존 파일을 읽고 사용자 피드백만 반영한다.

### 7. Phase 3: Integration

1. 모든 역할 산출물 읽기
2. 상충 정보 병기
3. 중복 제거
4. 누락된 역할이나 실패한 영역 표시
5. 최종 산출물 작성
   - 재실행 입력이나 역할 간 handoff는 `_workspace/machine/`에 둔다.
   - HUMAN 검토용 최종본은 `_workspace/human/`에 둔다.

데이터 흐름을 문서 안에 남긴다:

```text
[Input/context] -> [roles] -> [_workspace/machine role outputs] -> [integration] -> [_workspace/human final output]
```

### 8. Phase 4: Validation

검증 기준:

- 목표 충족 여부
- 역할별 산출물 누락 여부
- context/working rules 준수 여부
- 런타임별 파일 경로 정합성
- 보안 정보/API 키/인증 정보 작업 미포함
- 부분 재실행이면 영향받은 역할과 경계면만 다시 검증했는지 확인
- 런타임 delegation 없이 순차 fallback으로도 같은 파일 산출물이 나오는지 확인

검증 결과는 `_workspace/audit/qa_report.md`에 저장한다.

검증 판정은 다음 중 하나로 남긴다:

| Result | Meaning | Next step |
|---|---|---|
| PASS | 완료 조건을 충족 | Session Update로 이동 |
| FIX | 일부 수정으로 통과 가능 | 영향받은 역할만 재실행 |
| REJECT | 범위, 입력, 기준이 맞지 않아 진행 불가 | 사용자 확인 요청 |

동일한 FIX가 반복되면 재시도 횟수를 제한하고 unresolved issue로 보고한다.

역할 간 연결이 있는 하네스는 존재 확인보다 경계면 교차 비교를 우선한다. 예를 들어 API/프론트, 라우팅/링크, 상태 전이/업데이트 코드처럼 생산자와 소비자가 나뉘는 지점은 양쪽을 함께 읽는다.

### 9. Phase 5: Session Update

**아래 체크리스트를 완료하기 전에 작업 완료를 보고하지 않는다.**

작업 완료 후 다음 파일을 갱신한다. 각 파일의 형식은 `references/templates/core/context-writing-templates.md`의 해당 템플릿을 따른다:

- `HANDOFF.md`: 완료 작업, 결정 사항, 다음 할 일 (HANDOFF.md 템플릿 참조)
- `ROADMAP.md`: 진행 상황 (ROADMAP.md 템플릿 참조)
- `context/LESSONS.md`: 변경 이력과 학습 사항 (LESSONS.md 템플릿 참조)
- `BRIEF.md`: 세션 목표 달성 여부 (BRIEF.md 템플릿 참조)

pointer file에는 긴 변경 이력을 넣지 않는다.

세션 파일은 말로만 갱신했다고 보고하지 않는다. 실제 파일을 갱신하고, 갱신된 내용을 확인한다.

갱신 체크리스트:

- [ ] `HANDOFF.md`에 이번 세션 완료 작업, 결정 사항, 다음 할 일 기록
- [ ] `ROADMAP.md`에 진행 상태, 우선순위, 검증 계획 변경 반영
- [ ] `context/LESSONS.md`에 반복될 판단, 실수, 합의, 도메인 규칙 기록
- [ ] `BRIEF.md`에 세션 목표 달성 여부 기록
- [ ] `_workspace/` 목적별 폴더와 백업 위치 확인

체크리스트 항목을 모두 확인한 후에만 "작업 완료"를 보고한다. 갱신이 과한 작은 작업이면 어떤 파일을 갱신하지 않았는지와 이유를 짧게 남긴다. 하네스 생성, 수정, 감사, 대규모 실행은 기본적으로 갱신 대상이다.

## Failure Policy

| Situation | Strategy |
|---|---|
| One role fails | Retry once, then continue with explicit gap note when safe |
| Majority of roles fail | Stop and ask user whether to retry or narrow scope |
| Integration conflicts | Preserve both claims with source role names |
| Validation fails | Produce fix list and rerun only affected role when possible |
| Runtime tool unavailable | Fall back to file-based sequential execution |
| Timeout | Use collected partial results and mark incomplete scope |
| Phase dependency missing | Stop or rerun the missing upstream role |

## Mode Transition Rules

When execution shape changes between phases:

1. Save the current phase outputs under the appropriate `_workspace/` subfolder.
2. Record the next phase inputs.
3. Close or stop roles that are no longer needed when the runtime requires it.
4. Start the next phase roles with explicit file inputs.
5. Record transition decisions in `HANDOFF.md` when they affect future sessions.

## Test Scenarios

Each orchestrator should include:

1. Normal flow: input -> role execution -> integration -> validation -> session update
2. Partial rerun: existing `_workspace/` + targeted user feedback
3. Error flow: one role fails and final output marks missing scope
4. Validation flow: validation returns FIX and only affected roles rerun
5. Hybrid flow: one phase changes execution shape and uses file handoff

## Description Keywords

The orchestrator description must include initial and follow-up triggers:

- initial execution keywords
- rerun, update, modify, improve, supplement
- partial rerun expressions
- previous result based requests
- domain-specific everyday requests

Without follow-up keywords, the orchestrator is likely to be missed after the first run.
