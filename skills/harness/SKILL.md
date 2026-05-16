---
name: harness
description: "하네스를 구성, 점검, 재설계합니다. 프로젝트/도메인 요청을 런타임별 에이전트 역할, 스킬, 오케스트레이터, context 파일로 변환하는 메타 스킬입니다. '하네스 구성해줘', '하네스 구축', '하네스 설계', '하네스 점검/감사/현황', 'Codex용 하네스', 'Claude Code용 하네스', '멀티 런타임 하네스' 요청 시 사용합니다."
---

# Harness — Runtime-Aware Team & Skill Architect

결론부터: 이 스킬은 하네스의 **공통 설계 원칙**과 **런타임별 구현 차이**를 분리해서 적용한다.

## 읽기 순서

요청이 들어오면 필요한 문서만 단계적으로 읽는다.

1. 항상 `references/core/harness-principles.md`를 읽어 공통 원칙을 확인한다.
2. 역할/팀 구조를 설계할 때 `references/core/team-architecture-patterns.md`를 읽는다.
3. 스킬을 만들거나 수정할 때 `references/core/skill-authoring-overview.md`를 읽는다.
4. 에이전트 실행 context와 세션 연속성을 설계할 때 `references/core/context-protocol.md`를 읽는다.
5. 런타임을 정할 때 `references/runtimes/_runtime-matrix.md`를 읽는다.
6. 대상 런타임만 골라 읽는다:
   - Codex: `references/runtimes/codex/adapter.md`
   - Claude Code: `references/runtimes/claude-code/adapter.md`
7. 팀 아키텍처 패턴 상세가 필요하면 `references/core/team-patterns/{pattern}.md`와 선택한 런타임의 `execution-model.md`를 함께 읽는다.
8. 오케스트레이터를 작성할 때는 `references/templates/core/orchestrator.md`와 `references/templates/runtimes/{runtime}/orchestrator-overlay.md`를 함께 읽는다. 오케스트레이터의 Session Update Phase에 포함할 파일별 형식(HANDOFF.md, ROADMAP.md, LESSONS.md, BRIEF.md)은 `references/templates/core/context-writing-templates.md`에서 확인한다.
9. 상세 보충이 필요할 때만 추가 reference를 읽는다:
   - 스킬 작성 세부: `references/core/skill-authoring-details.md`
   - 스킬 테스트: `references/core/skill-testing.md`
   - QA 역할 검증: `references/core/qa-role-validation.md`
   - Claude Code 실행 모델: `references/runtimes/claude-code/execution-model.md`
   - Claude Code 예시: `references/examples/claude-code/team-examples.md`
   - Codex 실행 모델: `references/runtimes/codex/execution-model.md`
   - Codex 예시: `references/examples/codex/team-examples.md`

기존 reference에는 특정 런타임 전용 표현이 남아있을 수 있다. 그대로 복사하지 말고 `core / runtime-specific / context-protocol / template / obsolete`로 재판정한 뒤 사용한다.
하네스 생성·수정 시 다음 실행 품질 기준을 반드시 확인한다: 사용자 숙련도 감지, context 자동 채우기, `_workspace/` 보존과 부분 재실행, 역할별 파일 출력, 검증 루프, 트리거 near-miss, 세션 파일 실제 갱신.

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
3. 기존 문서의 내용을 `core`, `runtime-specific`, `context-protocol`, `template`, `example`, `obsolete`로 분류한다.
4. 신규 구축, 기존 확장, 운영/유지보수 중 하나로 분기한다.

기존 하네스가 있을 때는 전체를 다시 만들지 않는다. 변경 유형별로 필요한 Phase만 실행한다.

| 변경 유형 | Context | 공통 설계 | 역할 | 스킬 | Orchestrator | Pointer | 검증 |
|---|---|---|---|---|---|---|---|
| 신규 구축 | 생성/보완 | 필수 | 필수 | 필수 | 필수 | 필수 | 필수 |
| 역할 추가 | 보존 | 배치만 결정 | 추가 역할만 | 필요 시 | 필수 | 필요 시 | 필수 |
| 스킬 추가/수정 | 보존 | 건너뜀 | 연결만 확인 | 필수 | 연결 변경 시 | 필요 시 | 필수 |
| 아키텍처 변경 | 보존 | 필수 | 영향 범위 | 영향 범위 | 필수 | 필요 시 | 필수 |
| 감사/동기화 | 확인 | 불일치만 | 불일치만 | 불일치만 | 불일치만 | 확인 | 필수 |

감사 결과에는 최소한 다음을 남긴다:

- 오케스트레이터가 참조하지만 없는 역할/스킬
- 실제 파일은 있으나 오케스트레이터에서 쓰지 않는 역할/스킬
- pointer file의 런타임 불일치
- context/세션 파일 누락
- Claude Code 전용 도구명이나 Codex 전용 도구명이 잘못 섞인 위치

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

세부 규칙은 `references/core/context-protocol.md`와 `references/templates/core/context-writing-templates.md`를 따른다.

템플릿 상태의 파일은 사용자에게만 떠넘기지 말고, 현재 대화와 프로젝트 분석으로 알 수 있는 범위 안에서 먼저 채운다. 모르는 값은 추정하지 말고 `TBD` 또는 짧은 질문으로 남긴다. 이미 사용자가 채운 내용은 덮어쓰지 않는다.

### Phase 2: 공통 설계

런타임 경로를 정하기 전에 먼저 공통 설계를 만든다.

1. 목표와 도메인을 한 문장으로 정의한다.
2. 핵심 작업 유형을 식별한다.
3. 프로젝트 파일과 대화 맥락에서 사용자의 숙련도와 선호 설명 수준을 추정한다. 확실하지 않으면 추정하지 말고 쉬운 표현을 기본으로 둔다.
4. 기존 하네스와 충돌하거나 중복되는 역할/스킬이 있는지 확인한다.
5. 역할을 분리한다.
6. 아키텍처 패턴을 선택한다.
7. `references/core/team-patterns/`에서 해당 패턴 레퍼런스를 읽는다.
8. 역할별 입력/출력/검증 기준을 정의한다.
9. 파일 기반 산출물 경로를 `_workspace/` 기준으로 정한다.
10. `references/templates/core/orchestrator.md`를 읽고 오케스트레이터의 공통 Phase를 정한다.

### Phase 3: 런타임 어댑터 적용

선택한 런타임 문서를 읽고 다음 항목을 결정한다.

- pointer file 이름
- skill 출력 경로
- agent/role spec 출력 경로
- delegation 가능 여부와 구현 방식
- 모델명 또는 reasoning effort 표현
- 런타임에서 금지/주의해야 할 도구명
- 런타임별 execution model
- 런타임별 오케스트레이터 overlay

공통 문서에 런타임 전용 경로와 도구명을 섞지 않는다.

### Phase 4: 역할과 스킬 생성

1. 역할 문서는 “누가 무엇을 책임지는가”를 담는다.
2. 스킬 문서는 “어떻게 수행하는가”를 담는다.
3. 오케스트레이터는 “누가 언제 어떤 산출물을 넘기는가”를 담는다.
4. 모든 스킬은 description에 초기 실행, 후속 실행, 수정/보완 키워드를 포함한다.
5. 긴 세부 지식은 references로 분리한다.
6. 역할 문서에는 이전 산출물이 있을 때 읽고 개선하는 재호출 지침을 포함한다.
7. QA 역할이 필요한 하네스는 존재 확인보다 경계면 교차 비교를 책임으로 둔다.
8. 각 역할 문서에는 context 로딩, 입력/출력 프로토콜, 실패 처리, 협업 대상, 소유 범위를 포함한다.
9. 여러 역할이 공유하는 절차는 스킬로 분리하고, 한 역할 전용의 짧은 절차만 역할 문서에 인라인으로 둔다.
10. 런타임별 역할 파일은 반드시 adapter 경로에 생성한다. built-in type이나 worker/explorer를 쓰더라도 재사용 역할 스펙은 파일로 남긴다.
11. 역할별 모델명 또는 reasoning effort는 adapter 기준으로 정한다. 공통 설계 문서에는 특정 런타임 모델명을 넣지 않는다.

스킬을 만들 때는 다음을 확인한다:

- YAML frontmatter의 `name`, `description`이 있다.
- description은 보수적으로 트리거되는 상황을 고려해 구체적이고 적극적으로 쓴다.
- should-trigger와 should-not-trigger near-miss를 생각하며 경계가 모호한 요청을 구분한다.
- `SKILL.md` 본문은 lean하게 유지하고, 500줄에 가까워지면 세부 지식은 `references/`로 분리한다.
- 반복 코드나 결정적 처리는 가능하면 `scripts/`로 분리한다.
- 스킬은 원리와 판단 기준을 설명하고, 특정 예시에만 과적합하지 않는다.

### Phase 5: 오케스트레이터와 Pointer File 등록

오케스트레이터는 개별 역할/스킬을 하나의 실행 흐름으로 묶는다.

1. 신규 구축이면 오케스트레이터 스킬을 만든다.
2. 기존 확장이면 새로 만들지 말고 기존 오케스트레이터의 역할 목록, 데이터 흐름, description 트리거를 수정한다.
3. Phase별 실행 형태를 명시한다. 하이브리드면 Phase마다 실행 형태와 이유를 표로 남긴다.
4. 역할 간 데이터 전달 방식을 정한다: 파일 기반 `_workspace/`, 런타임 메시지, 작업 상태, 반환 요약 중 무엇을 쓰는지 명시한다.
5. 모든 역할의 입력이 이전 Phase 산출물 또는 사용자 입력과 연결되는지 확인한다.
6. 실패 정책을 포함한다: 1회 재시도, 누락 표시, 상충 정보 보존, 영향받은 역할만 재실행.
7. 오케스트레이터에는 정상 흐름, 부분 재실행, 에러 흐름, 검증 FIX 흐름의 테스트 시나리오를 포함한다.

런타임별 pointer file에는 최소 정보만 넣는다.

- 하네스 이름과 목표
- 오케스트레이터 스킬 트리거
- context 참조 규칙
- 완료 후 세션 파일 갱신 규칙

에이전트 전체 목록, 긴 실행 절차, 변경 이력은 pointer file에 넣지 않는다.

### Phase 6: 검증

`references/core/harness-validation-checklist.md` 기준으로 검증한다.

필수 확인:

- 런타임별 파일 경로가 맞는가
- pointer file이 맞는가
- 오케스트레이터 참조와 실제 역할/스킬 파일이 일치하는가
- 공통 문서에 런타임 전용 도구명이 섞이지 않았는가
- 보안 정보, API 키, 인증 정보 작업이 포함되지 않았는가
- 역할별 입력/출력 경로가 끊기지 않는가
- 실행 형태가 Phase별로 명시됐는가
- `_workspace/` 보존, 부분 재실행, 새 실행 분기가 오케스트레이터 Phase 0에 있는가
- QA 역할 또는 검증 Phase가 존재 확인이 아니라 경계면 교차 비교를 수행하는가
- pointer file에 하네스 포인터와 context 참조 규칙이 있고, 긴 실행 절차는 없는가

스킬을 새로 만들거나 크게 고쳤으면 `references/core/skill-testing.md` 기준으로 최소 대표 프롬프트와 near-miss 트리거 검증을 수행한다. 비용이나 런타임 제약으로 실행 테스트를 생략하면 생략 사유와 미검증 범위를 남긴다.

스킬 테스트는 가능하면 다음 순서로 수행한다:

1. 대표 테스트 프롬프트 2-3개 작성
2. with-skill과 baseline 또는 이전 iteration 비교
3. should-trigger와 should-not-trigger near-miss 검증
4. 드라이런으로 Phase 순서, 데이터 흐름, fallback 경로 확인
5. 실패가 발견되면 특정 예시에만 맞춘 수정이 아니라 일반화된 규칙으로 개선
6. 재검증 결과와 미검증 범위 기록

### Phase 7: 진화와 유지보수

하네스는 한 번 만들고 끝나는 산출물이 아니다. 사용자 피드백, 반복 실패, 수동 우회 작업이 보이면 하네스 개선을 제안한다. 변경 사항은 `context/LESSONS.md`와 `HANDOFF.md`에 기록한다.

실행 완료 후에는 사용자를 압박하지 않는 선에서 개선 피드백 기회를 제공한다. 같은 피드백이 반복되거나 에이전트가 같은 방식으로 실패하거나 사용자가 오케스트레이터를 우회해 수동 처리하면 하네스 수정 대상으로 본다.

운영/유지보수 요청이면 다음 순서로 처리한다:

1. 현황 감사 결과를 `_workspace/harness_audit.md`에 남긴다.
2. 사용자 요청 범위 안에서 역할, 스킬, context, pointer file을 점진적으로 수정한다.
3. 각 수정 뒤 오케스트레이터 연결과 트리거를 다시 확인한다.
4. `context/LESSONS.md` 변경 이력에 날짜, 변경 내용, 대상, 사유를 기록한다.
5. 대규모 변경이면 드라이런과 트리거 검증까지 수행한다.

## 산출물 체크리스트

런타임별 정확한 경로는 `references/runtimes/{runtime}/adapter.md`를 따른다.

- [ ] 공통 설계 요약
- [ ] context/ 파일과 세션 파일
- [ ] pointer file
- [ ] 역할/agent spec
- [ ] 역할별 skill
- [ ] orchestrator skill
- [ ] `_workspace/` 산출물 규칙
- [ ] 구조 검증 결과
- [ ] 오케스트레이터 데이터 흐름과 테스트 시나리오
- [ ] 트리거 검증 예시
- [ ] 실행 또는 드라이런 테스트 결과
- [ ] 부분 재실행/후속 실행 경로
- [ ] 세션 파일 실제 갱신 확인
- [ ] 변경 이력

## 금지

- 환경변수, API 키, 인증 정보 생성/수집/변경 절차를 만들지 않는다.
- 특정 런타임 도구명을 다른 런타임 산출물에 그대로 복사하지 않는다.
- 기존 문서를 단순 경로 치환으로 마이그레이션하지 않는다.
