# Context Writing Templates

Context protocol이 사용하는 프로젝트 context 파일과 세션 파일의 런타임 중립 템플릿이다.

## ABOUT-ME.md

```markdown
# About Me

## 신원
- 역할: {{역할}}
- 회사/팀: {{회사 또는 팀}}
- 도메인: {{도메인}}

## 커뮤니케이션 선호도
- 사용자 대면 언어: {{언어}}
- 문서 작성 언어: {{언어}}
- 설명 깊이: {{시니어/주니어/비개발자 등}}

## 작업 스타일
- {{작업 스타일}}
```

## BRAND-VOICE.md

```markdown
# Brand Voice

## 톤
- {{톤 1}}
- {{톤 2}}
- {{톤 3}}

## 언어
- 사용자 대면 커뮤니케이션: {{언어/어투}}
- 문서/코드 주석: {{언어/어투}}

## 금지 패턴
| 패턴 | 예시 | 선호 |
|---|---|---|
| {{패턴}} | "{{나쁜 예시}}" | "{{선호}}" |

## 보이스 예시
| 상황 | 하지 말 것 | 할 것 |
|---|---|---|
| {{상황}} | "{{나쁜 예시}}" | "{{좋은 예시}}" |
```

## WORKING-RULES.md

```markdown
# Working Rules

MUST/SHOULD는 팀과 도메인에 맞게 교체한다. 단, Context 관리 규칙은 세션 간 품질 유지를 위해 유지한다.

## Context 관리 규칙
1. 작업 전 `ROADMAP.md`로 장기 방향을 확인한다.
2. `BRIEF.md`로 이번 세션의 목표, 범위, 완료 조건을 확인한다.
3. 작업에 필요한 도메인/사용자/문체 정보가 있으면 `context/` 아래 관련 파일을 찾아 읽는다.
4. 작업 완료 후 `HANDOFF.md`를 갱신한다.
5. 목표, 범위, 우선순위, 진행 상태, 검증 계획이 바뀐 경우 `ROADMAP.md`를 갱신한다.
6. 반복될 가능성이 있는 판단, 실수, 팀 합의, 도메인 규칙을 배운 경우 `context/LESSONS.md`에 기록한다.
7. API 키, 토큰, 비밀번호, 인증 정보 등 민감정보는 생성/수집/수정/노출하지 않는다.

## MUST
1. {{팀이 반드시 지킬 규칙}}

## SHOULD
1. {{팀이 권장하는 규칙}}
```

## GLOSSARY.md

```markdown
# Glossary

## 핵심 개념
| 용어 | 정의 |
|---|---|
| {{용어}} | {{정의}} |

## 파일
| 파일 | 목적 |
|---|---|
| BRIEF.md | 이번 세션 목표 |
| HANDOFF.md | 다음 세션 인계 |
| ROADMAP.md | 장기 계획 |
```

## LESSONS.md

```markdown
# Lessons

## 기록 기준
- 반복될 가능성이 있는 판단, 실수, 팀 합의, 도메인 규칙을 기록한다.
- 단순 작업 로그나 일회성 진행 상황은 `HANDOFF.md`에 남긴다.

## 변경 이력
| 날짜 | 변경 내용 | 대상 | 사유 |
|---|---|---|---|
| {{YYYY-MM-DD}} | 초기 구성 | 전체 | - |

## 학습 사항
### {{YYYY-MM-DD}} - {{제목}}
- 맥락:
- 학습:
- 조치:
```

## BRIEF.md

```markdown
# Brief

## 작성 기준
- 세션 시작 시 이번 세션의 목표, 범위, 완료 조건이 명확하지 않으면 작성한다.
- 단순 질의나 즉시 끝나는 작업은 생략할 수 있다.

## 초점
{{이번 세션 목표 한 줄}}

## 목표
1. {{검증 가능한 목표}}
2. {{검증 가능한 목표}}

## 제약사항
- {{제약}}

## 완료 조건
- [ ] {{조건}}
```

## HANDOFF.md

```markdown
# Handoff

## 이번 세션
- 날짜:
- 범위:
- 상태:

### 완료
1.
2.

### 결정
-

## 다음 세션
1.
2.
```

## ROADMAP.md

```markdown
# Roadmap

## 작성 기준
- 여러 세션에 걸쳐 목표, 범위, 우선순위, 진행 상태를 추적해야 할 때 작성한다.
- 작업이 한 세션 안에 끝나기 어렵거나 다음 세션의 의사결정에 영향을 주면 작성한다.
- 단발성 작업은 `HANDOFF.md`에 다음 작업만 남기고 `ROADMAP.md`는 생략할 수 있다.

## 목표
1.
2.

## 비목표
1.

## 진행 상황
- [x] 완료된 것
- [ ] 다음 작업

## 검증
1.
```

## Pointer File Section

Pointer file 이름은 runtime adapter를 따른다.

```markdown
## Harness: {{domain}}

목표: {{목표 한 줄}}

트리거: {{도메인}} 관련 다단계 작업, 하네스 실행/수정/점검 요청 시 {{orchestrator-skill}}을 사용한다.

## Context 참조 규칙
1. 작업 전 `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 읽는다.
2. 상세한 Context 관리 기준은 `context/WORKING-RULES.md`를 따른다.
3. 작업에 필요한 도메인/사용자/문체 정보가 있으면 `context/` 아래 관련 파일을 찾아 읽는다.
4. 작업 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`, `BRIEF.md` 갱신 필요 여부를 확인한다.
```

Pointer file에는 역할/스킬 전체 목록, 긴 실행 절차, 변경 이력을 넣지 않는다. 런타임별 세부 템플릿은 `references/runtimes/{runtime}/adapter.md`를 따른다.
