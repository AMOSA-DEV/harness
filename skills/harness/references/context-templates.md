# Context Templates

결론부터: 템플릿은 시작점이다. 프로젝트에서 확인한 내용만 남기고 불필요한 섹션은 삭제한다.

## `AGENTS.md`

```md
# Project Instructions

## Project
- 목적: <확인된 프로젝트 목적>
- 주요 경로: <작업에 중요한 경로>

## Rules
- <반복 적용할 핵심 규칙>

## Verification
- <완료 전 확인할 명령 또는 기준>

## Context
- 작업 전 `ROADMAP.md`와 필요한 `context/` 파일을 읽는다.
- 일반 작업은 `ROADMAP.md`를 직접 수정하지 않는다.
- 병렬 작업 상태는 승인된 경우 `_workspace/machine/tasks/`에 분리한다.
```

확인되지 않은 빌드 명령, 도구, 디렉터리는 만들지 않는다.

## `ROADMAP.md`

```md
# Roadmap

## Project Goal

<프로젝트 전체 목표>

## In Progress

- 없음

## Next

- 없음

## Done

- 없음
```

일반 작업 대화는 읽기만 한다. 통합 갱신을 요청받은 조정 작업은 각 task 파일과 실제 산출물을 확인한 뒤 상태를 바꾼다.

## `context/ABOUT-ME.md`

```md
# About Me

## Role

<확인된 사용자 또는 팀의 역할>

## Collaboration Preferences

- <확인된 협업 선호>
```

## `context/BRAND-VOICE.md`

```md
# Brand Voice

## Voice

- <확인된 문체>

## Avoid

- <확인된 금지 표현>
```

외부 문서나 사용자 대면 콘텐츠가 없는 프로젝트라면 빈 파일을 만들기보다 생성을 생략할 수 있다.

## `context/WORKING-RULES.md`

```md
# Working Rules

## Must

- <모든 작업에 반복 적용할 규칙>

## Ask First

- 환경변수, API 키, 인증 정보 관련 작업

## Done When

- <프로젝트 공통 완료 기준>
```

## `context/GLOSSARY.md`

```md
# Glossary

| Term | Meaning |
|---|---|
| <용어> | <프로젝트에서 사용하는 의미> |
```

도메인 용어나 약어가 없으면 생성을 생략할 수 있다.

## `context/LESSONS.md`

```md
# Lessons

반복할 가치가 있는 학습만 기록한다.

## Entries

- 없음
```

각 항목에는 날짜, 배운 점, 다음 작업에서 적용할 규칙을 짧게 기록한다. 실행 로그와 단발성 오류는 넣지 않는다.

## `_workspace/`

사용자 승인 후에만 만든다.

```text
_workspace/
├── audit/
├── human/
└── machine/
    └── tasks/
```

## Task 파일

```md
# <작업 이름>

- ID: YYYYMMDD-task-name
- 상태: 진행 중
- 범위: <이 작업이 다루는 범위>
- 충돌 가능성: <겹칠 수 있는 파일 또는 영역, 없으면 없음>
- 결과 위치: <완료 산출물 경로, 미정이면 없음>
- 후속 작업: <필요한 후속 작업, 없으면 없음>
```

task 파일은 해당 작업 대화만 수정한다. 완료하면 상태와 실제 결과 위치를 갱신한다.
