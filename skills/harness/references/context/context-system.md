# Context System

컨텍스트 시스템은 런타임과 무관하게 세션별 context를 참조해 작업을 이어받고 일관된 품질의 결과를 내도록 돕는다.

## 표준 파일

| 파일 | 목적 |
|---|---|
| `context/ABOUT-ME.md` | 사용자/팀의 역할, 선호, 작업 방식 |
| `context/BRAND-VOICE.md` | 말투, 스타일, 금지 표현 |
| `context/WORKING-RULES.md` | MUST/SHOULD 규칙 |
| `context/GLOSSARY.md` | 도메인 용어 |
| `context/LESSONS.md` | 변경 이력과 학습 사항 |
| `BRIEF.md` | 현재 세션 목표 |
| `HANDOFF.md` | 완료 작업과 다음 세션 인계 |
| `ROADMAP.md` | 장기 계획 |

## Pointer File

런타임별 pointer file은 하네스의 존재와 context를 읽기 시작하는 최소 규칙만 담는다.

| Runtime | Pointer file |
|---|---|
| Codex | `AGENTS.md` |
| Claude Code | `CLAUDE.md` |

pointer file에 에이전트 목록, 스킬 목록, 긴 실행 규칙, 상세한 Context 관리 기준을 모두 넣지 않는다. 그것들은 오케스트레이터, 스킬/역할 문서, `context/WORKING-RULES.md`가 관리한다.

## Context 참조 규칙

pointer file에는 다음 부트스트랩 규칙을 런타임에 맞게 넣는다:

1. 작업 전 `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 읽는다.
2. 상세한 Context 관리 기준은 `context/WORKING-RULES.md`를 따른다.
3. 작업에 필요한 도메인/사용자/문체 정보가 있으면 `context/` 아래 관련 파일을 찾아 읽는다.
