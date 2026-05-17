# Codex Team Examples

Codex 고유 실행 방식을 보여주는 참고 문서다. 공통 패턴은 `references/core/team-patterns/`와 `references/templates/core/`를 먼저 읽는다.

## Research Fan-out/Fan-in

역할:

| Role | 권장 실행 | Scope | Output |
|---|---|---|---|
| official-researcher | explorer 또는 메인 세션 | 공식 문서/블로그 | `_workspace/machine/research_official.md` |
| media-researcher | explorer 또는 메인 세션 | 미디어/투자 | `_workspace/machine/research_media.md` |
| community-researcher | explorer 또는 메인 세션 | 커뮤니티/SNS | `_workspace/machine/research_community.md` |
| background-researcher | explorer 또는 메인 세션 | 배경/경쟁/학술 | `_workspace/machine/research_background.md` |

Codex 구현 포인트:

- 독립 조사 역할은 explorer/worker 후보로 분리하고, 메인 세션이 결과를 통합한다.
- 직접 팀 통신은 보장하지 않는다. 상호 영향 정보는 메인 세션이 `send_input`으로 중계하거나 `_workspace/machine/notes.md`에 기록한다.
- 각 역할은 자기 산출물을 `_workspace/machine/`에 남긴다.
- 통합자는 모든 파일을 읽고 출처, 상충 정보, 미검증 범위를 병기한다.
- delegation을 쓰지 않는 실행에서는 메인 세션이 역할별 heading을 유지하며 순차 조사한다.

## Novel Team Hybrid

역할:

| Role | 권장 실행 | Scope | Output |
|---|---|---|---|
| worldbuilder | 메인 세션 또는 worker | 세계관, 물리/사회/기술 토대 | `_workspace/machine/01_worldbuilder_setting.md` |
| character-designer | 메인 세션 또는 worker | 인물, 계급, 관계 | `_workspace/machine/01_character_profiles.md` |
| plot-architect | 메인 세션 또는 worker | 플롯 구조, 갈등, 장면 흐름 | `_workspace/machine/01_plot_outline.md` |
| prose-stylist | 메인 세션 또는 worker | 초안 작성과 수정 | `_workspace/human/02_prose_draft.md` |
| science-consultant | 메인 세션 또는 reviewer | 과학 검증 | `_workspace/audit/03_science_review.md` |
| continuity-manager | 메인 세션 또는 reviewer | 설정/장면 일관성 검증 | `_workspace/audit/03_continuity_review.md` |

Codex 구현 포인트:

- 직접 팀 토론을 전제로 하지 않는다. 메인 세션이 각 역할 산출물을 읽고 충돌을 정리한다.
- Phase 1의 설정/캐릭터/플롯 역할은 독립성이 높아 delegation 후보가 된다.
- Phase 2 집필은 세 산출물을 모두 입력으로 받아 단일 역할이 수행한다.
- Phase 3 검토는 `science-consultant`와 `continuity-manager`가 각각 `FIX` 목록과 미검증 범위를 남긴다.
- 과학 검증이 설정 일관성에 영향을 주면 메인 세션이 continuity 쪽에 요약해 전달한다.

## Webtoon Producer-Reviewer

역할:

| Role | 권장 실행 | Scope | Output |
|---|---|---|---|
| webtoon-artist | worker 또는 메인 세션 | 패널 생성 또는 재생성 | `_workspace/human/panels/` |
| webtoon-reviewer | explorer/reviewer 또는 메인 세션 | 구도, 캐릭터 일관성, 텍스트 가독성 검수 | `_workspace/audit/review_report.md` |

Codex 구현 포인트:

- producer와 reviewer가 같은 패널 파일을 동시에 수정하지 않게 한다.
- reviewer는 패널별 `PASS`, `FIX`, `REDO`를 명확히 남긴다.
- `FIX`나 `REDO`는 artist가 바로 실행할 수 있는 수정 지시를 포함한다.
- `FIX`면 메인 세션이 수정 범위를 판단하고 artist 재실행 또는 직접 수정을 선택한다.
- 같은 패널 재생성은 최대 2회처럼 제한하고, 반복 실패는 unresolved issue로 남긴다.
- 전체 패널의 절반 이상이 `REDO`이면 프롬프트나 요구사항 자체를 사용자에게 되묻는다.

## Code Review Fan-out

역할:

| Role | 권장 실행 | Scope | Output |
|---|---|---|---|
| security | explorer 또는 worker | 인증, 권한, 주입, secret 노출 | `_workspace/audit/review_security.md` |
| performance | explorer 또는 worker | 쿼리, 렌더링, 캐시, N+1 | `_workspace/audit/review_performance.md` |
| test | explorer 또는 worker | 테스트 누락, fixture, CI 리스크 | `_workspace/audit/review_tests.md` |

Codex 구현 포인트:

- 파일 수정 없는 리뷰는 explorer/read-only 성격의 worker를 우선한다.
- 수정까지 맡기려면 worker별 소유 파일 범위를 겹치지 않게 나눈다.
- 한 reviewer의 발견이 다른 reviewer의 범위에 영향을 주면 메인 세션이 요약해 전달한다.
- 예: 보안 이슈가 성능 이슈와 연결되면 메인 세션이 performance에게 관련 정보를 전달한다.
- 예: 성능 이슈가 테스트 누락과 연결되면 메인 세션이 test에게 관련 케이스 확인을 요청한다.
- 최종 보고서는 severity, 파일, 근거, 수정 방향, 미검증 범위를 포함한다.
- 보안 정보나 인증 정보 처리는 리뷰 대상 위험으로만 다루고, 생성/수집/변경 절차를 만들지 않는다.

## Migration Supervisor

역할:

| Role | 권장 실행 | Scope | Output |
|---|---|---|---|
| supervisor | 메인 세션 | 파일 목록, batch, 의존성, 통합 | `_workspace/machine/task-ledger.md` |
| worker-n | worker 또는 메인 세션 | 할당된 파일 묶음 처리 | `_workspace/machine/migration_worker_n.md` |

Codex 구현 포인트:

- 메인 세션이 supervisor가 되어 대상 파일, batch, 우선순위, 충돌 가능성을 정리한다.
- worker를 쓸 때는 각 worker의 소유 파일을 disjoint set으로 나눈다.
- 같은 파일을 여러 worker가 동시에 수정하게 설계하지 않는다.
- worker 프롬프트에는 “다른 작업자가 있을 수 있음”, “관련 없는 변경 되돌리지 않음”, “변경 파일 목록 보고”를 포함한다.
- worker 실패 시 같은 worker 1회 재시도 후 재할당한다.
- 새 작업이 발견되면 메인 세션이 작업 목록에 추가한다.
- 모든 worker 결과를 모은 뒤 메인 세션이 통합 검증과 `git diff --check` 같은 구조 검사를 수행한다.

## 메인 세션 Fallback 예시

```text
1. Phase 0: context와 `_workspace/` 상태 확인
2. Phase 1: 역할별 작업 패킷 작성
3. Phase 2A: official-researcher 역할을 메인 세션에서 수행 -> `_workspace/machine/research_official.md`
4. Phase 2B: media-researcher 역할을 메인 세션에서 수행 -> `_workspace/machine/research_media.md`
5. Phase 2C: community-researcher 역할을 메인 세션에서 수행 -> `_workspace/machine/research_community.md`
6. Phase 3: 통합 보고서 작성
7. Phase 4: `references/core/harness-validation-checklist.md` 기준 검증
8. Phase 5: HANDOFF/ROADMAP/LESSONS/BRIEF 갱신 필요 여부 확인
```
