# Claude Code Team Examples

기존 예시에서 Claude Code 고유 실행 방식만 보존한 참고 문서다. 공통 패턴은 `references/templates/core/`를 먼저 읽는다.

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
- 흥미로운 발견은 필요한 팀원에게만 메시지로 공유한다.
- durable output은 반드시 `_workspace/`에 남긴다.
- 통합자는 모든 파일을 읽고 상충 정보를 병기한다.

## Code Review Fan-out

역할:

| Role | Scope |
|---|---|
| security | 인증, 권한, 주입, secret 노출 |
| performance | 쿼리, 렌더링, 캐시, N+1 |
| test | 테스트 누락, fixture, CI 리스크 |

Claude Code 구현 포인트:

- 발견이 다른 관점에 영향을 주면 직접 메시지로 공유한다.
- 예: 보안 이슈가 성능 이슈와 연결되면 security가 performance에게 알린다.
- 최종 보고서는 severity, 파일, 근거, 수정 방향을 포함한다.

## Migration Supervisor

역할:

| Role | Scope |
|---|---|
| supervisor | 파일 목록, 배치, 의존성, 통합 |
| worker-n | 할당된 파일 묶음 처리 |

Claude Code 구현 포인트:

- 공유 작업 목록이 가능하면 작업 단위를 등록한다.
- 워커 실패 시 같은 워커 1회 재시도 후 재할당한다.
- 새 작업이 발견되면 supervisor가 작업 목록에 추가한다.
