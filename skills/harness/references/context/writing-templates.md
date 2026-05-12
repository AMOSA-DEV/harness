# Context Writing Templates

프로젝트 루트의 context 파일과 세션 파일을 작성할 때 쓰는 런타임 중립 템플릿이다.

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

*최종 업데이트: {{YYYY-MM-DD}}*
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

## MUST
1. 중간 산출물은 `_workspace/` 아래에 저장한다.
2. 파일명은 `{phase}_{role}_{artifact}.{ext}` 형식을 사용한다.
3. 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`를 갱신한다.
4. 보안 정보, API 키, 인증 정보 작업은 하지 않는다.

## SHOULD
1. 변경 이유를 짧게 기록한다.
2. 새 규칙을 추가하면 검증 방법도 함께 적는다.
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
1. 세션 시작 시 `context/` 아래 Markdown 파일을 확인한다.
2. 작업 전 `BRIEF.md`와 `context/WORKING-RULES.md`를 읽는다.
3. 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`를 갱신한다.
```
