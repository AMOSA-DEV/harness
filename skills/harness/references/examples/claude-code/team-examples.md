# Claude Code Team Examples

Claude Code 고유 실행 방식을 보여주는 참고 문서다. 공통 패턴은 `references/core/team-patterns/`와 `references/templates/core/`를 먼저 읽는다.

## Research Fan-out/Fan-in

역할:

| Role | Type | Scope | Output |
|---|---|---|---|
| official-researcher | `general-purpose` | 공식 문서/블로그 | `_workspace/research_official.md` |
| media-researcher | `general-purpose` | 미디어/투자 | `_workspace/research_media.md` |
| community-researcher | `general-purpose` | 커뮤니티/SNS | `_workspace/research_community.md` |
| background-researcher | `general-purpose` | 배경/경쟁/학술 | `_workspace/research_background.md` |

Claude Code 구현 포인트:

- Agent Teams가 가능하면 팀으로 구성한다.
- `TeamCreate`로 조사 팀을 만들고, `TaskCreate`로 조사 범위를 나눠 등록한다.
- 흥미로운 발견은 필요한 팀원에게만 메시지로 공유한다.
- 상충 정보가 나오면 팀원끼리 직접 토론하되, 최종 통합자는 출처와 근거를 병기한다.
- durable output은 반드시 `_workspace/`에 남긴다.
- 통합자는 모든 파일을 읽고 상충 정보를 병기한다.

통신 예시:

```text
official -> background: 관련 공식 발표 공유
media -> background: 투자/인수 정보 공유
community -> media: 커뮤니티 반응 중 미디어 관련 정보 공유
all members -> shared tasks: 진행률 업데이트
```

## Novel Team Hybrid

역할:

| Role | Scope | Runtime shape | Output |
|---|---|---|---|
| worldbuilder | 세계관, 물리/사회/기술 토대 | Team member | `_workspace/01_worldbuilder_setting.md` |
| character-designer | 인물, 계급, 관계 | Team member | `_workspace/01_character_profiles.md` |
| plot-architect | 플롯 구조, 갈등, 장면 흐름 | Team member | `_workspace/01_plot_outline.md` |
| prose-stylist | 초안 작성과 수정 | Subagent or sequential role | `_workspace/02_prose_draft.md` |
| science-consultant | 과학 검증 | Team member | `_workspace/03_science_review.md` |
| continuity-manager | 설정/장면 일관성 검증 | Team member | `_workspace/03_continuity_review.md` |

Claude Code 구현 포인트:

- Phase 1은 `worldbuilder`, `character-designer`, `plot-architect`를 팀으로 묶어 병렬 실행한다.
- `worldbuilder`가 사회 구조를 정하면 `character-designer`에게, 주요 갈등을 정하면 `plot-architect`에게 메시지로 전달한다.
- Phase 2는 팀을 정리한 뒤 `prose-stylist`를 단독 호출한다. 단독 집필은 팀 조율보다 파일 입력이 중요하다.
- Phase 3은 `science-consultant`와 `continuity-manager`로 새 리뷰 팀을 만든다. 물리 오류가 설정 일관성에 영향을 주면 서로 공유한다.
- Phase 4는 리뷰 결과 파일을 입력으로 `prose-stylist`가 수정한다.
- 세션당 활성 팀을 하나로 제한해야 하는 환경에서는 Phase 전환마다 `_workspace/`에 산출물을 저장하고 팀을 재구성한다.

## Webtoon Producer-Reviewer

역할:

| Role | Scope | Runtime shape | Output |
|---|---|---|---|
| webtoon-artist | 패널 생성 또는 재생성 | Subagent or sequential role | `_workspace/panels/` |
| webtoon-reviewer | 구도, 캐릭터 일관성, 텍스트 가독성 검수 | Subagent or sequential role | `_workspace/review_report.md` |

Claude Code 구현 포인트:

- 생성-검증 역할이 2개뿐이고 직접 토론보다 결과 전달이 중요하면 Agent Teams보다 단독 `Agent` 호출이 적합하다.
- reviewer는 패널별 `PASS`, `FIX`, `REDO`를 명확히 남긴다.
- `FIX`나 `REDO`는 artist가 바로 실행할 수 있는 수정 지시를 포함한다.
- 같은 패널 재생성은 최대 2회처럼 제한을 둔다.
- 전체 패널의 절반 이상이 `REDO`이면 프롬프트나 요구사항 자체를 사용자에게 되묻는다.

## Code Review Fan-out

역할:

| Role | Scope |
|---|---|
| security | 인증, 권한, 주입, secret 노출 |
| performance | 쿼리, 렌더링, 캐시, N+1 |
| test | 테스트 누락, fixture, CI 리스크 |

Claude Code 구현 포인트:

- `TeamCreate(review-team)`로 관점별 리뷰어를 묶고, 각 리뷰어에게 독립 작업을 준다.
- 발견이 다른 관점에 영향을 주면 직접 메시지로 공유한다.
- 예: 보안 이슈가 성능 이슈와 연결되면 security가 performance에게 알린다.
- 예: 성능 이슈가 테스트 누락과 연결되면 performance가 test에게 관련 케이스 확인을 요청한다.
- 최종 보고서는 severity, 파일, 근거, 수정 방향을 포함한다.

## Migration Supervisor

역할:

| Role | Scope |
|---|---|
| supervisor | 파일 목록, 배치, 의존성, 통합 |
| worker-n | 할당된 파일 묶음 처리 |

Claude Code 구현 포인트:

- 공유 작업 목록이 가능하면 작업 단위를 등록한다.
- supervisor는 파일 목록, 복잡도, 의존성을 보고 batch를 나눈다.
- worker는 작업을 claim하거나 할당받고, `TaskUpdate`로 완료/실패를 보고한다.
- 워커 실패 시 같은 워커 1회 재시도 후 재할당한다.
- 새 작업이 발견되면 supervisor가 작업 목록에 추가한다.
- 모든 작업 완료 뒤 supervisor가 통합 검증을 실행한다.
