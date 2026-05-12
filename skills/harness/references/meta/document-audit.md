# Document Audit

기존 루트 reference 문서를 단순 이동하지 않고 내용 기준으로 재판정한 기록이다.

| 기존 문서 | 고유 내용 | 처리 |
|---|---|---|
| `SKILL.md` | Claude Code 런타임 설명이 본문에 과도하게 섞임 | 런타임 라우터로 경량화 |
| `agent-design-patterns.md` | Claude Code Agent Teams, built-in 타입, 모델 매핑 | 공통 패턴은 `core/architecture-patterns.md`, Claude 고유 내용은 `runtimes/claude-code/agent-teams.md` |
| `orchestrator-template.md` | 오케스트레이터 Phase 구조와 Claude 실행 primitive | 공통 구조는 `templates/core/orchestrator.md`, 실행 primitive는 runtime overlay |
| `team-examples.md` | Claude Code 팀 예시 | `examples/claude-code/team-examples.md`로 축약 |
| `skill-writing-guide.md` | 상세 description, schema, 검증 판정 규칙 | `core/skill-writing-details.md` |
| `skill-testing-guide.md` | with-skill/baseline, assertion, trigger eval | `core/skill-testing.md` |
| `context-system-guide.md` | context 구조와 참조 규칙 | `context/context-system.md` |
| `context-writing-templates.md` | context/세션 파일 템플릿 | `context/writing-templates.md` |
| `qa-agent-guide.md` | 경계면 교차 검증 방법론 | `core/qa-validation.md` |
| `team-architecture-templates/*.md` | 패턴 템플릿과 Claude 도구명 혼재 | `templates/core/`와 runtime overlay |

## 원칙

- 중복 설명은 새 core 문서에 반복하지 않았다.
- Claude Code 도구명은 Claude runtime 문서나 Claude examples에만 남겼다.
- context 템플릿은 pointer file 이름을 직접 쓰지 않고 runtime adapter를 따르게 했다.
- 루트 `references/`에는 일반 문서를 남기지 않고, 감사 기록은 `references/meta/`로 이동했다.
