# Context System

컨텍스트 시스템은 런타임과 무관하게 다음 세션이 작업을 이어받도록 돕는다.

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

런타임별 pointer file은 하네스의 존재와 context 참조 규칙만 담는다.

| Runtime | Pointer file |
|---|---|
| Codex | `AGENTS.md` |
| Claude Code | `CLAUDE.md` |

pointer file에 에이전트 목록, 스킬 목록, 긴 실행 규칙을 모두 넣지 않는다. 그것들은 오케스트레이터와 스킬/역할 문서가 관리한다.

## Context 참조 규칙

pointer file에는 다음 규칙을 런타임에 맞게 넣는다:

1. 세션 시작 시 `context/` 아래 모든 Markdown 파일을 훑는다.
2. 작업 수행 전 `BRIEF.md`와 `context/WORKING-RULES.md`를 확인한다.
3. 도메인 용어가 나오면 `context/GLOSSARY.md`를 확인한다.
4. 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`를 갱신한다.
5. 보안 정보, API 키, 인증 정보는 생성/수집/수정하지 않는다.
