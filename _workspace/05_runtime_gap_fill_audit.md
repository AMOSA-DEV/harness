# Runtime Gap Fill Audit

날짜: 2026-05-14

## 범위

`origin/main`의 Claude Code 중심 하네스 문서와 현재 `dev`의 런타임 분리 결과물을 비교했다. 목표는 원본의 실행 품질 규칙을 여러 런타임을 지원하는 구조에 맞게 재배치하는 것이다.

## 보강한 누락 영역

| 영역 | 원본 핵심 | 반영 위치 |
|---|---|---|
| 사용자 숙련도 감지 | 대화와 프로젝트 단서로 설명 수준 조절 | `SKILL.md`, `core/context-protocol.md`, `templates/core/orchestrator.md` |
| Context 자동 채우기 | 템플릿만 만들지 않고 아는 범위는 먼저 채움 | `SKILL.md`, `core/context-protocol.md` |
| 후속/부분 재실행 | `_workspace/` 기준 초기/부분/새 실행 분기 | `SKILL.md`, `templates/core/orchestrator.md`, `templates/runtimes/codex/orchestrator-overlay.md` |
| 세션 파일 실제 갱신 | 말로 보고하지 않고 파일 수정 후 확인 | `core/context-protocol.md`, `core/harness-validation-checklist.md`, `templates/core/orchestrator.md` |
| 스킬 실행 테스트 | with-skill vs baseline, near-miss, 최소 검증 | `core/skill-testing.md`, `core/harness-validation-checklist.md`, `SKILL.md` |
| 검증 루프 | PASS/FIX/REJECT, 재시도 제한, 실제 변경 확인 | `core/skill-authoring-details.md`, `templates/core/orchestrator.md` |
| Codex worker 안전성 | 역할 스펙과 실제 subagent 타입 분리, 소유 범위 명시 | `runtimes/codex/adapter.md`, `runtimes/codex/execution-model.md`, `templates/runtimes/codex/orchestrator-overlay.md` |
| 팀 크기/작업 단위 | 역할 수와 역할당 작업량 기준 | `core/team-architecture-patterns.md` |

## 확인한 정리 사항

- 삭제된 원본 reference 경로를 현재 문서에서 참조하지 않도록 확인했다.
- Claude Code 도구명은 Claude runtime 문서와 runtime matrix에서만 의미 있게 등장하고, Codex 문서에서는 금지/대체 대상으로만 등장한다.
- 런타임 중립 orchestrator 문서는 runtime-specific `orchestrator-overlay.md`를 읽도록 수정했다.

## 남은 주의점

- 실제 샘플 프로젝트에 설치해 trigger eval과 드라이런을 수행하면 더 확실하다.
- 현재 문서는 여러 런타임을 지원하는 구조를 목표로 하며 Claude Code adapter와 Codex adapter를 모두 보존한다. 둘 중 하나만 배포할 때는 README와 plugin metadata의 포지셔닝을 다시 맞춘다.
