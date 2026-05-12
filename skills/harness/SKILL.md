---
name: harness
description: "하네스를 구성, 점검, 재설계합니다. 프로젝트/도메인 요청을 런타임별 에이전트 역할, 스킬, 오케스트레이터, context 파일로 변환하는 메타 스킬입니다. '하네스 구성해줘', '하네스 구축', '하네스 설계', '하네스 점검/감사/현황', 'Codex용 하네스', 'Claude Code용 하네스', '멀티 런타임 하네스' 요청 시 사용합니다."
---

# Harness — Runtime-Aware Team & Skill Architect

결론부터: 이 스킬은 하네스의 **공통 설계 원칙**과 **런타임별 구현 차이**를 분리해서 적용한다.

## 읽기 순서

요청이 들어오면 필요한 문서만 단계적으로 읽는다.

1. 항상 `references/core/harness-principles.md`를 읽어 공통 원칙을 확인한다.
2. 역할/팀 구조를 설계할 때 `references/core/architecture-patterns.md`를 읽는다.
3. 스킬을 만들거나 수정할 때 `references/core/skill-generation.md`를 읽는다.
4. context/세션 파일을 만들거나 갱신할 때 `references/context/context-system.md`를 읽는다.
5. 런타임을 정할 때 `references/runtimes/_runtime-matrix.md`를 읽는다.
6. 대상 런타임만 골라 읽는다:
   - Codex: `references/runtimes/codex.md`
   - Claude Code: `references/runtimes/claude-code.md`
7. 아키텍처 템플릿이 필요하면 `references/templates/core/{pattern}.md`를 먼저 읽고, 선택한 런타임의 overlay를 함께 읽는다:
   - Codex: `references/templates/runtimes/codex/overlay.md`
   - Claude Code: `references/templates/runtimes/claude-code/overlay.md`
8. 상세 보충이 필요할 때만 추가 reference를 읽는다:
   - 스킬 작성 세부: `references/core/skill-writing-details.md`
   - 스킬 테스트: `references/core/skill-testing.md`
   - QA 검증: `references/core/qa-validation.md`
   - 컨텍스트 템플릿: `references/context/writing-templates.md`
   - Claude Code 팀 semantics: `references/runtimes/claude-code/agent-teams.md`
   - Claude Code 예시: `references/examples/claude-code/team-examples.md`
   - 감사 기록: `references/meta/document-audit.md`

기존 reference에는 Claude Code 전용 표현이 남아있을 수 있다. 그대로 복사하지 말고 `core / runtime-specific / context / stale`로 재판정한 뒤 사용한다.

## 런타임 선택

사용자가 런타임을 명시하면 그것을 따른다.

| 사용자 표현 | 런타임 |
|---|---|
| Codex, OpenAI Codex, AGENTS.md, `.codex` | Codex |
| Claude Code, CLAUDE.md, `.claude` | Claude Code |
| 멀티 런타임, 둘 다 | 공통 설계 1개 + 런타임별 산출물 |

명시가 없으면 현재 실행 환경과 프로젝트 파일을 보고 추정한다. 잘못된 런타임 파일을 생성할 위험이 크면 짧게 확인한다.

## 워크플로우

### Phase 0: 현황 감사

1. 현재 프로젝트의 pointer file, skill 디렉토리, agent 디렉토리, context 파일을 확인한다.
2. 기존 하네스가 있으면 오케스트레이터가 참조하는 역할/스킬과 실제 파일을 비교한다.
3. 기존 문서의 내용을 `core`, `runtime-specific`, `context`, `example`, `stale/remove`로 분류한다.
4. 신규 구축, 기존 확장, 운영/유지보수 중 하나로 분기한다.

### Phase 1: 컨텍스트 초기화

신규 구축이면 다음 파일을 만든다. 이미 있으면 보존하고 빈 템플릿만 보완한다.

- `context/ABOUT-ME.md`
- `context/BRAND-VOICE.md`
- `context/WORKING-RULES.md`
- `context/GLOSSARY.md`
- `context/LESSONS.md`
- `BRIEF.md`
- `HANDOFF.md`
- `ROADMAP.md`

세부 규칙은 `references/context/context-system.md`와 `references/context/writing-templates.md`를 따른다.

### Phase 2: 공통 설계

런타임 경로를 정하기 전에 먼저 공통 설계를 만든다.

1. 목표와 도메인을 한 문장으로 정의한다.
2. 핵심 작업 유형을 식별한다.
3. 역할을 분리한다.
4. 아키텍처 패턴을 선택한다.
5. `references/templates/core/`에서 해당 패턴 템플릿을 읽는다.
6. 역할별 입력/출력/검증 기준을 정의한다.
7. 파일 기반 산출물 경로를 `_workspace/` 기준으로 정한다.
8. `references/templates/core/orchestrator.md`를 읽고 오케스트레이터의 공통 Phase를 정한다.

### Phase 3: 런타임 어댑터 적용

선택한 런타임 문서를 읽고 다음 항목을 결정한다.

- pointer file 이름
- skill 출력 경로
- agent/role spec 출력 경로
- delegation 가능 여부와 구현 방식
- 모델명 또는 reasoning effort 표현
- 런타임에서 금지/주의해야 할 도구명
- 런타임별 템플릿 overlay
- 런타임별 오케스트레이터 overlay

공통 문서에 런타임 전용 경로와 도구명을 섞지 않는다.

### Phase 4: 역할과 스킬 생성

1. 역할 문서는 “누가 무엇을 책임지는가”를 담는다.
2. 스킬 문서는 “어떻게 수행하는가”를 담는다.
3. 오케스트레이터는 “누가 언제 어떤 산출물을 넘기는가”를 담는다.
4. 모든 스킬은 description에 초기 실행, 후속 실행, 수정/보완 키워드를 포함한다.
5. 긴 세부 지식은 references로 분리한다.

### Phase 5: Pointer File 등록

런타임별 pointer file에는 최소 정보만 넣는다.

- 하네스 이름과 목표
- 오케스트레이터 스킬 트리거
- context 참조 규칙
- 완료 후 세션 파일 갱신 규칙

에이전트 전체 목록, 긴 실행 절차, 변경 이력은 pointer file에 넣지 않는다.

### Phase 6: 검증

`references/core/validation.md` 기준으로 검증한다.

필수 확인:

- 런타임별 파일 경로가 맞는가
- pointer file이 맞는가
- 오케스트레이터 참조와 실제 역할/스킬 파일이 일치하는가
- 공통 문서에 런타임 전용 도구명이 섞이지 않았는가
- 보안 정보, API 키, 인증 정보 작업이 포함되지 않았는가

### Phase 7: 진화와 유지보수

사용자 피드백, 반복 실패, 수동 우회 작업이 보이면 하네스 개선을 제안한다. 변경 사항은 `context/LESSONS.md`와 `HANDOFF.md`에 기록한다.

## 산출물 체크리스트

런타임별 정확한 경로는 `references/runtimes/{runtime}.md`를 따른다.

- [ ] 공통 설계 요약
- [ ] context/ 파일과 세션 파일
- [ ] pointer file
- [ ] 역할/agent spec
- [ ] 역할별 skill
- [ ] orchestrator skill
- [ ] `_workspace/` 산출물 규칙
- [ ] 구조 검증 결과
- [ ] 트리거 검증 예시
- [ ] 변경 이력

## 금지

- 환경변수, API 키, 인증 정보 생성/수집/변경 절차를 만들지 않는다.
- 특정 런타임 도구명을 다른 런타임 산출물에 그대로 복사하지 않는다.
- 기존 문서를 단순 경로 치환으로 마이그레이션하지 않는다.
