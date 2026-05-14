# References Flow Audit

날짜: 2026-05-15

## 검토 범위

`skills/harness/references/`의 문서 흐름, 파일 간 참조, 런타임 경계, 원본 reference 대비 재배치 상태를 확인했다.

## 판단

- 읽기 흐름은 `SKILL.md`에서 `core` 공통 원칙을 먼저 읽고, 필요 시 `runtime adapter`, `runtime overlay`, `template`, `example`로 내려가는 구조라 자연스럽다.
- `core/`와 `templates/core/`에서는 Claude Code 또는 Codex 도구명, 경로, 모델명을 직접 요구하지 않는다.
- 런타임별 도구명과 경로는 `references/runtimes/`와 `references/templates/runtimes/`에 배치되어 있다.
- 원본 reference의 핵심 내용은 현재 문서로 재배치되어 있다.
  - `agent-design-patterns.md` -> `core/team-architecture-patterns.md`, `core/team-patterns/`, `runtimes/claude-code/execution-model.md`, runtime overlays
  - `context-system-guide.md` -> `core/context-protocol.md`, `templates/core/context-writing-templates.md`, runtime pointer templates
  - `context-writing-templates.md` -> `templates/core/context-writing-templates.md`
  - `orchestrator-template.md` -> `templates/core/orchestrator.md`, runtime orchestrator overlays
  - `qa-agent-guide.md` -> `core/qa-role-validation.md`
  - `skill-testing-guide.md` -> `core/skill-testing.md`, `core/harness-validation-checklist.md`
  - `skill-writing-guide.md` -> `core/skill-authoring-details.md`, `core/skill-authoring-overview.md`
  - `team-examples.md` -> `examples/claude-code/team-examples.md`

## 수정

- `core/validation.md`는 필요한 검증 컨텍스트지만 파일명이 너무 일반적이라 `core/harness-validation-checklist.md`로 변경했다.
- `SKILL.md`, `_workspace/05_runtime_gap_fill_audit.md`, `HANDOFF.md`의 참조를 새 이름으로 갱신했다.
- runtime overlay 문서와 `_runtime-matrix.md`의 영어 중심 문구를 한국어 중심 톤으로 정리했다.
- Codex 전용 보강 문서로 `runtimes/codex/execution-model.md`와 `examples/codex/team-examples.md`를 추가했다.
- 중복처럼 보이던 검증 문서는 역할을 분리했다. `harness-validation-checklist.md`는 하네스 전체 구조/트리거/드라이런/세션 갱신 검증이고, `qa-role-validation.md`는 생성되는 QA 역할의 경계면 교차 검증 책임이다.
- 서로 따로 노는 이름처럼 보이던 스킬 작성 문서는 `skill-authoring-overview.md`와 `skill-authoring-details.md`로 이름을 맞췄다.

## 남은 주의점

- 실제 Codex/Claude Code 런타임에서 하네스 생성까지 실행 검증은 아직 하지 않았다.
