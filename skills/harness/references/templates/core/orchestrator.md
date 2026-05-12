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
- 적용한 core architecture template
- 적용한 runtime overlay
- delegation 가능 여부

### 3. Role Map

| Role | Responsibility | Skill | Input | Output |
|---|---|---|---|---|
| {role} | {responsibility} | {skill-name} | {input} | `_workspace/{phase}_{role}_{artifact}.md` |

### 4. Phase 0: State Check

1. pointer file 존재 여부 확인
2. context/세션 파일 존재 여부 확인
3. `_workspace/` 존재 여부 확인
4. 실행 유형 결정:
   - `_workspace/` 없음: 초기 실행
   - `_workspace/` 있음 + 부분 수정 요청: 부분 재실행
   - `_workspace/` 있음 + 새 입력: 이전 workspace를 timestamp 폴더로 보존 후 새 실행

### 5. Phase 1: Preparation

1. 사용자 입력과 목표 확인
2. context 파일 읽기
3. `_workspace/00_input/`에 입력 스냅샷 저장
4. 역할별 작업 패킷 작성

### 6. Phase 2: Role Execution

선택한 architecture template에 따라 역할을 실행한다.

필수 규칙:

- 모든 역할은 입력과 출력 경로가 명확해야 한다.
- 큰 산출물은 반환값보다 파일에 저장한다.
- 실패한 역할이 있으면 최종 산출물에 누락을 표시한다.
- 런타임이 병렬 실행을 지원하지 않으면 순차 실행한다.

### 7. Phase 3: Integration

1. 모든 역할 산출물 읽기
2. 상충 정보 병기
3. 중복 제거
4. 최종 산출물 작성

### 8. Phase 4: Validation

검증 기준:

- 목표 충족 여부
- 역할별 산출물 누락 여부
- context/working rules 준수 여부
- 런타임별 파일 경로 정합성
- 보안 정보/API 키/인증 정보 작업 미포함

검증 결과는 `_workspace/qa_report.md`에 저장한다.

### 9. Phase 5: Session Update

작업 완료 후 다음 파일을 갱신한다:

- `HANDOFF.md`: 완료 작업, 결정 사항, 다음 할 일
- `ROADMAP.md`: 진행 상황
- `context/LESSONS.md`: 변경 이력과 학습 사항
- `BRIEF.md`: 세션 목표 달성 여부

pointer file에는 긴 변경 이력을 넣지 않는다.

## Failure Policy

| Situation | Strategy |
|---|---|
| One role fails | Retry once, then continue with explicit gap note when safe |
| Majority of roles fail | Stop and ask user whether to retry or narrow scope |
| Integration conflicts | Preserve both claims with source role names |
| Validation fails | Produce fix list and rerun only affected role when possible |
| Runtime tool unavailable | Fall back to file-based sequential execution |

## Test Scenarios

Each orchestrator should include:

1. Normal flow: input -> role execution -> integration -> validation -> session update
2. Partial rerun: existing `_workspace/` + targeted user feedback
3. Error flow: one role fails and final output marks missing scope
