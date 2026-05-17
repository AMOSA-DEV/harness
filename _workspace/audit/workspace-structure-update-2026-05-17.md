# Workspace Structure Update Audit

## 결론

`_workspace/` 산출물 규칙을 목적별 3개 폴더로 표준화했다.

## 변경 기준

| 폴더 | 용도 |
|---|---|
| `_workspace/audit/` | 하네스 감사 결과, QA 리포트, 구조 검증 |
| `_workspace/machine/` | 재작업, 부분 재실행, 역할 간 handoff용 산출물 |
| `_workspace/human/` | HUMAN 검토용 최종 산출물 |

## 백업 계획

- 새 실행으로 기존 `_workspace/`를 보존해야 하면 `_workspace_archive/YYYYMMDD_HHMMSS/` 또는 프로젝트 표준 timestamp 경로에 전체 백업한다.
- 백업 전에는 기존 산출물을 삭제하지 않는다.
- 백업 위치와 재사용할 핵심 파일은 `_workspace/audit/` 감사 메모 또는 `HANDOFF.md`에 기록한다.
- 기존 루트 산출물은 즉시 강제 이동하지 않고, 새 산출물부터 표준 폴더를 사용한다.

## 수정 파일

- `skills/harness/SKILL.md`
- `skills/harness/references/core/harness-principles.md`
- `skills/harness/references/core/harness-validation-checklist.md`
- `skills/harness/references/templates/core/orchestrator.md`
- `skills/harness/references/runtimes/codex/adapter.md`
- `skills/harness/references/runtimes/codex/execution-model.md`
- `skills/harness/references/templates/runtimes/codex/orchestrator-overlay.md`
- `skills/harness/references/runtimes/claude-code/adapter.md`
- `skills/harness/references/templates/runtimes/claude-code/orchestrator-overlay.md`

## 검증 메모

- 감사 결과 경로는 `_workspace/audit/`로 이동했다.
- 역할별 machine handoff 경로는 `_workspace/machine/`으로 이동했다.
- HUMAN 검토용 최종본 경로는 `_workspace/human/`으로 분리했다.
- runtime adapter와 orchestrator overlay의 백업 경로는 `_workspace_archive/YYYYMMDD_HHMMSS/` 기준으로 맞췄다.
