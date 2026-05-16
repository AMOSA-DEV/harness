# Claude Code Runtime Adapter

이 문서는 Claude Code에서 하네스를 구현할 때만 읽는다.

상세 실행 방식은 `references/runtimes/claude-code/execution-model.md`를 따른다.

## Output Paths

| 산출물 | 위치 |
|---|---|
| Pointer file | `CLAUDE.md` |
| Project skill | `.claude/skills/{skill-name}/SKILL.md` |
| Agent definition | `.claude/agents/{agent-name}.md` |
| Orchestrator skill | `.claude/skills/{orchestrator-name}/SKILL.md` |
| Runtime workspace | `_workspace/` |

## Adapter Rules

- Agent Teams가 활성화된 환경에서는 `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`를 사용할 수 있다.
- Agent Teams가 비활성화된 환경에서는 파일 기반 순차 실행 또는 `Agent(..., run_in_background=true)` 패턴으로 대체한다.
- `SendMessage({to: "all"})` 브로드캐스트는 비용이 높으므로 드물게 사용한다.
- 세션당 활성 팀은 하나로 보고, Phase 전환 시 `_workspace/`에 산출물을 저장한 뒤 팀을 재구성한다.
- 팀원은 자기 팀을 다시 생성하지 않는다. 중첩 구조가 필요하면 평탄화하거나 2단계는 subagent 호출로 구현한다.
- 모든 재사용 역할은 `.claude/agents/{name}.md`에 정의한다.
- 스킬은 `.claude/skills/{name}/SKILL.md`에 둔다.
- Claude Code 산출물은 `CLAUDE.md`의 로컬 지침을 우선 적용해야 한다.

## Delegation Mapping

| Core 개념 | Claude Code 구현 |
|---|---|
| Specialist | `.claude/agents/{name}.md` + `Agent` 또는 Team member |
| Parallel fan-out | `TeamCreate` + `TaskCreate`, 또는 background `Agent` |
| Reviewer | 팀원 또는 별도 `Agent` |
| Direct communication | `SendMessage` |
| Shared task state | `TaskCreate` / `TaskUpdate` |
| Shared workspace | `_workspace/{phase}_{role}_{artifact}.md` |
| Handoff | `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md` 갱신 |

## Model Mapping

| 복잡도 | 모델 |
|---|---|
| 단순 반복/형식 검수 | `haiku` |
| 일반 분석/작성/구현 | `sonnet` |
| 복잡한 설계/추론/창작 | `opus` |

## CLAUDE.md Pointer Template

```markdown
## 하네스: {domain}

목표: {목표 한 줄}

트리거: {도메인} 관련 다단계 작업, 하네스 실행, 하네스 수정/점검 요청 시 `{orchestrator-skill-name}` 스킬을 사용한다.

## Context 참조 규칙
1. 작업 전 `ROADMAP.md`, `BRIEF.md`, `context/WORKING-RULES.md`를 읽는다.
2. 상세한 Context 관리 기준은 `context/WORKING-RULES.md`를 따른다.
3. 작업에 필요한 도메인/사용자/문체 정보가 있으면 `context/` 아래 관련 파일을 찾아 읽는다.
4. 작업 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`, `BRIEF.md` 갱신 필요 여부를 확인한다.
```

CLAUDE.md에는 역할 전체 목록, 긴 실행 절차, 변경 이력을 넣지 않는다. 그 내용은 `.claude/agents/`, `.claude/skills/`, `context/LESSONS.md`가 관리한다.
