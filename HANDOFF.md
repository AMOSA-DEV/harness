# Handoff

## 현재 작업 맥락

- 작업 저장소: `harness/`
- 현재 브랜치: `dev`
- 비교 기준: `origin/main`
- 목표: Claude Code 원본 하네스를 Codex로 전환하는 것이 아니라, 원본의 의미와 실행 품질을 보존하면서 여러 런타임을 지원할 수 있도록 구조를 재분리한다.
- 핵심 문제: 첫 런타임 추상화 과정에서 원본 세부 규칙이 과도하게 압축되거나, 문서 위치가 어색해진 부분이 있다.

## 완료된 작업

1. 원본 Claude Code 중심 문서를 `core / runtime adapter / template / example / obsolete` 관점으로 재분류하는 방향을 잡았다.
2. Codex도 지원되도록 `references/runtimes/codex/adapter.md`, `_runtime-matrix.md`, Codex overlay 문서를 보강했다.
3. 원본에서 누락되기 쉬운 context 자동 채우기, 부분 재실행, `_workspace/` 보존, with-skill vs baseline 테스트, 세션 파일 실제 갱신 규칙을 복구했다.
4. 삭제된 원본 reference 경로가 현재 문서에 남아있는지 확인했다.
5. 이전 보강 감사 메모를 `_workspace/05_runtime_gap_fill_audit.md`에 남겼다.
6. `references/examples/claude-code/team-examples.md`에 원본의 SF 소설 팀, 웹툰 생성-검증, 팀 통신, 동적 작업 배정 예시를 Claude Code 전용 실행 예시로 복원했다.
7. `.codex-plugin/plugin.json`과 `_workspace/05_runtime_gap_fill_audit.md`의 “Codex 우선/중심”으로 읽히는 표현을 여러 런타임 지원 포지셔닝으로 수정했다.
8. `core/harness-validation-checklist.md`에 있던 런타임별 실제 경로 열거를 runtime adapter 참조 방식으로 옮겨 core/runtime 경계를 정리했다.
9. core/templates 경로에서 런타임 전용 도구명과 삭제된 reference 경로가 남아있는지 재확인했다.
10. `git diff --check`를 실행했고 whitespace/error 경고 없이 통과했다.
11. runtime adapter와 runtime overlay의 책임을 재검토했다. Adapter는 경로/제약/모델/기본 템플릿, overlay는 패턴별 적용과 오케스트레이터 실행 방식으로 구분되어 있어 치명적 중복은 없다고 판단했다.
12. `origin/main`의 삭제된 reference 파일을 실제 기준 경로(`agent-design-patterns.md`, `context-system-guide.md`, `context-writing-templates.md`, `orchestrator-template.md`, `qa-agent-guide.md`, `skill-testing-guide.md`, `skill-writing-guide.md`, `team-examples.md`)로 대조했고, 핵심 규칙이 현재 core/runtime/template/example 문서에 재배치된 것을 확인했다.
13. `/private/tmp/harness-sample-project`에 Codex 샘플 설치 형태로 `skills/harness`를 복사하고, trigger 문구와 Codex 드라이런 경로(`AGENTS.md`, `.codex/skills`, `.codex/agents`, `_workspace`, 부분 재실행, 세션 갱신)를 정적으로 확인했다.
14. `references/` 흐름을 재검토했고, `validation.md`처럼 범위가 모호한 새 파일명을 `core/harness-validation-checklist.md`로 바꿨다. core/templates에는 런타임 전용 도구명 하드코딩이 없음을 확인했다.
15. runtime overlay 문서와 `_runtime-matrix.md`의 영어 중심 문구를 한국어 중심 톤으로 정리했다.
16. Codex 전용 보강 문서로 `references/runtimes/codex/execution-model.md`와 `references/examples/codex/team-examples.md`를 추가하고 `SKILL.md` 읽기 순서에 연결했다.
17. 중복처럼 보이던 검증 문서를 재명명해 역할을 분리했다. `core/harness-validation-checklist.md`는 하네스 전체 검증, `core/qa-role-validation.md`는 생성되는 QA 역할 검증이다.
18. 연결된 스킬 작성 문서명을 `core/skill-authoring-overview.md`와 `core/skill-authoring-details.md`로 맞췄다.
19. `core/qa-role-validation.md`를 원본 `qa-agent-guide.md` 성격에 맞게 조정했다. SatangSlide 웹 앱 사례 기반 문서임을 명시하고, 웹 앱 체크리스트는 범용 QA가 아니라 도메인에 맞을 때만 가져오는 예시로 분리했다.
20. `skills/harness/references/`에서 작업 보고서/원본 비교 표현을 재검색하고, `core/qa-role-validation.md`, `examples/claude-code/team-examples.md`, runtime semantics 문장의 최종 reference 톤을 정리했다. 삭제된 reference 경로 재검색과 `git diff --check`도 통과했다.
21. `references/examples/claude-code/team-examples.md`와 `references/examples/codex/team-examples.md`가 같은 예시 축으로 비교되도록 Codex 문서를 `Research`, `Novel`, `Webtoon`, `Code Review`, `Migration` 순서와 역할 구성에 맞췄다.
22. 런타임 문서 파일명을 `runtimes/{runtime}/adapter.md`와 `runtimes/{runtime}/execution-model.md`로 통일하고, `_runtime-matrix.md`에 Codex/Claude Code 용어 차이를 정규화해 반영했다.
23. Claude Code와 Codex adapter 문서의 품질과 범위를 맞추기 위해 adapter는 경로/규칙/매핑/pointer template 중심으로 정리하고, Codex agent spec/worker prompt 템플릿은 `codex/execution-model.md`로 옮겼다.
24. `templates/runtimes/*/overlay.md`는 실행 모델과 중복되어 제거했다. 패턴 매핑은 각 런타임 `execution-model.md`로 옮기고, templates runtime 문서는 orchestrator template overlay만 남겼다.
25. 현재 `SKILL.md`를 `origin/main`의 원본과 대조했다. reference 분리로 보존된 내용은 많지만 Phase 4-7이 과도하게 압축된 부분이 있어 역할/스킬 생성, 오케스트레이션, 검증/테스트, 진화 기준을 top-level에 다시 보강했다.
26. 현재 스킬의 문서 참조와 런타임 경계 최종 검증을 수행했다. 예전 `references/runtimes/{runtime}.md` 참조 2곳과 모호한 체크리스트 참조 1곳을 수정했고, 결과를 `_workspace/07_final_skill_validation.md`에 기록했다.

## 중요한 원칙

- Codex 우선 전환이 아니다. Codex도 지원하는 런타임 추상화 개선이다.
- Claude Code adapter도 정상 지원 대상으로 보존한다.
- 공통 원칙은 `references/core/`에 둔다.
- 런타임 도구, 경로, 모델명은 `references/runtimes/` 또는 `references/templates/runtimes/`에 둔다.
- 템플릿성 내용은 `references/templates/`에 둔다.
- 특정 런타임 예시는 `references/examples/{runtime}/`에 둔다.
- 원본 내용을 단순 삭제하거나 과도하게 요약하지 않는다.
- 단, 최종 산출물인 `skills/harness/references/` 문서 안에는 “원본에서는”, “이전 작업에서”, “복원했다”, “재배치했다” 같은 작업 보고서/비교 메모를 남기지 않는다. 그런 내용은 `HANDOFF.md`나 `_workspace/` 감사 메모에만 둔다.
- 보안 관련 작업, 환경변수, API 키, 인증 정보는 다루지 않는다.

## 다음 세션 최우선 작업

실제 Codex/Claude Code 런타임에서 하네스 생성까지 실행 검증한다.

반드시 확인할 것:

1. 현재 `skills/harness/references/`는 최종 reference 톤으로 정리된 상태다. 작업 보고서 표현은 다시 생긴 부분만 좁혀서 확인한다.
2. Codex 런타임에서 샘플 하네스 생성 플로우를 실행해 pointer file, `.codex/skills`, `.codex/agents`, `_workspace`, context/session 갱신이 실제로 맞물리는지 본다.
3. Claude Code 런타임은 실제 실행이 어렵다면 최소한 adapter 산출물 경로와 overlay 지시가 Claude Code semantics와 충돌하지 않는지 검증한다.
4. 검증 결과는 `_workspace/` 감사 메모에 남기고, `HANDOFF.md`의 남은 의심 지점을 갱신한다.
5. 수정 후 `git diff --check`를 실행한다.

## 다음 세션 시작 프롬프트

```text
현재 작업은 `harness/` 내부 git 저장소의 `dev` 브랜치에서 진행 중이다.

먼저 `harness/HANDOFF.md`를 읽고, 이전 세션에서 완료한 작업과 남은 의심 지점을 파악하라.
그 다음 필요할 때만 `_workspace/05_runtime_gap_fill_audit.md`, `_workspace/06_references_flow_audit.md`, `_workspace/07_final_skill_validation.md`를 읽어 세부 보강 내역을 확인하라.

목표:
Claude Code 원본 하네스를 Codex로 전환하는 작업이 아니다.
원본 하네스의 의미와 실행 품질을 보존하면서, Claude Code와 Codex 등 여러 런타임을 지원할 수 있도록 `core / runtime adapter / template / example / obsolete` 구조를 계속 검증하고 개선하는 것이다.

현재 문제:
정적 문서 정리는 대부분 끝났다. 남은 핵심 리스크는 실제 Codex/Claude Code 런타임에서 하네스 생성까지 실행했을 때 adapter, overlay, context/session 갱신 규칙이 자연스럽게 맞물리는지 아직 확인하지 못했다는 점이다.

중요 원칙:
- Codex 우선 전환이라고 표현하지 말 것.
- Claude Code adapter도 보존한다.
- 공통 원칙은 `references/core/`에 둔다.
- 런타임 도구/경로/모델명은 `references/runtimes/` 또는 runtime overlay에 둔다.
- 템플릿성 내용은 `references/templates/`에 둔다.
- 특정 런타임 예시는 `references/examples/{runtime}/`에 둔다.
- 현재 구조와 충돌하거나 중복되는 내용만 `obsolete`로 본다.
- 최종 산출물인 `references/` 본문에는 원본 비교/작업 보고 표현을 쓰지 않는다.
- 보안 관련 작업, 환경변수, API 키, 인증 정보는 다루지 않는다.

우선 읽을 파일:
1. `harness/HANDOFF.md`
2. `harness/_workspace/05_runtime_gap_fill_audit.md`가 있으면 읽는다.
3. 필요한 경우에만 아래 핵심 파일을 읽는다:
   - `harness/skills/harness/SKILL.md`
   - `harness/skills/harness/references/core/harness-principles.md`
   - `harness/skills/harness/references/core/team-architecture-patterns.md`
   - `harness/skills/harness/references/core/context-protocol.md`
   - `harness/skills/harness/references/templates/core/orchestrator.md`
   - `harness/skills/harness/references/runtimes/_runtime-matrix.md`
   - `harness/skills/harness/references/runtimes/claude-code/adapter.md`
   - `harness/skills/harness/references/runtimes/codex/adapter.md`

비교 기준:
- 원본 기준은 `origin/main`
- 현재 작업물은 현재 체크아웃된 `dev`
- 필요한 경우에만 `git show origin/main:<path>` 또는 `git diff origin/main -- <path>`를 사용한다.
- 저장소 전체 탐색은 피하고, `HANDOFF.md`, 이전 감사 메모, 핵심 문서에서 의심 지점을 좁힌다.

이번 세션에서 해야 할 일:
1. `HANDOFF.md`를 읽고 다음 세션 최우선 작업을 따른다.
2. 실제 Codex 런타임에서 샘플 하네스 생성 플로우를 실행하거나, 실행이 제한되면 제한 사유와 대체 검증을 기록한다.
3. Claude Code 런타임 산출물은 실제 실행 또는 정적 adapter 검증으로 확인한다.
4. 실행 중 발견한 문제만 좁게 수정하고, core/runtime/template/example 경계를 유지한다.
5. 검증 결과를 `_workspace/` 감사 메모와 `HANDOFF.md`에 남긴다.
6. `git diff --check`를 실행한다.

응답 규칙:
- 한국어.
- 결론 먼저.
- 짧게.
```

## 남은 의심 지점

- [x] 원본 `team-examples.md`의 실제 예시가 새 `references/examples/claude-code/`에 충분히 보존됐는가.
- [x] README와 plugin metadata가 “Codex 전환”이 아니라 “여러 런타임 지원”으로 읽히는가.
- [x] core 문서가 너무 추상적이라 실제 하네스 생성자가 바로 실행하기 어려운 부분은 없는가.
- [x] runtime adapter와 runtime overlay의 책임이 중복되거나 어긋나는 부분은 없는가.
- [x] 삭제된 원본 reference의 세부 규칙이 현재 문서에 모두 적절히 재배치됐는가.
- [x] 실제 샘플 프로젝트에 설치해 trigger eval과 드라이런을 수행했는가.
- [ ] 실제 Codex/Claude Code 런타임에서 하네스 생성까지 실행 검증했는가.
- [x] runtime overlay 문서의 영문/국문 문체를 전체 문서 톤에 맞춰 정리할 필요가 있는가.
- [x] `references/` 본문에 작업 보고서/원본 비교 표현이 남아있지 않은가.

## 세션 종료 시 갱신 규칙

새 세션에서 의미 있는 수정이나 판단을 했다면 이 파일을 갱신한다.

- 완료된 작업에 새 항목 추가
- 남은 의심 지점 체크 또는 추가
- 새로 만든 감사 파일 경로 기록
- 다음 세션 시작 프롬프트가 바뀌어야 하면 함께 수정
