# Claude Code Agent Teams

이 문서는 Claude Code 전용 에이전트 팀 semantics를 보존한다.

## Team Mode

팀 리더가 팀을 구성하고 팀원은 독립 Claude Code 인스턴스로 실행된다. 팀원은 직접 메시지를 주고받고 공유 작업 목록으로 조율할 수 있다.

특징:

- 팀원 간 직접 대화, 도전, 검증 가능
- 공유 작업 목록으로 자체 조율
- 팀원이 유휴 상태가 되면 리더에게 알림
- Phase 사이에 팀을 정리하고 새 팀을 구성할 수 있음

제약:

- 세션당 한 팀만 활성화 가능
- 중첩 팀 불가
- 리더 이전 불가
- 토큰 비용이 높음

## Subagent Mode

메인 에이전트가 단발성 에이전트를 호출하고 결과를 메인에게만 반환받는다.

특징:

- 가볍고 빠름
- 결과 수집이 단순
- 에이전트 간 직접 통신 없음

## Claude Built-in Types

| 타입 | 도구 접근 | 적합한 용도 |
|---|---|---|
| `general-purpose` | 전체 도구 | 웹 조사, 범용 작업, 검증 실행 |
| `Explore` | 읽기 전용 | 코드베이스 탐색, 분석 |
| `Plan` | 읽기 전용 | 아키텍처 설계, 계획 |

재사용 역할은 빌트인 타입을 쓰더라도 `.claude/agents/{name}.md`에 역할 정의를 남긴다.

## Model Mapping

| 모델 | 사용 기준 |
|---|---|
| `haiku` | 단순 반복, 형식 검수 |
| `sonnet` | 일반 분석, 작성, 구현 |
| `opus` | 복잡한 설계, 추론, 고품질 창작 |

## Agent Definition Skeleton

```markdown
---
name: agent-name
description: "역할 설명과 트리거"
---

# Agent Name

## 핵심 역할
1.

## 작업 원칙
-

## 입력/출력 프로토콜
- 입력:
- 출력:

## Context 로딩
1. `context/` 아래 Markdown 파일을 확인한다.
2. BRAND-VOICE, WORKING-RULES, GLOSSARY를 우선 참조한다.

## 팀 통신 프로토콜
- 메시지 수신:
- 메시지 발신:
- 작업 요청:
```
