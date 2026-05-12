# Claude Code Runtime Adapter

이 문서는 Claude Code에서 하네스를 구현할 때만 읽는다.

## Output Paths

| 산출물 | 위치 |
|---|---|
| Pointer file | `CLAUDE.md` |
| Project skill | `.claude/skills/{skill-name}/SKILL.md` |
| Agent definition | `.claude/agents/{agent-name}.md` |
| Orchestrator skill | `.claude/skills/{orchestrator-name}/SKILL.md` |
| Runtime workspace | `_workspace/` |

## Claude Code 제약

- Agent Teams가 활성화된 환경에서는 `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`를 사용할 수 있다.
- Agent Teams가 비활성화된 환경에서는 파일 기반 순차 실행 또는 `Agent(..., run_in_background=true)` 패턴으로 대체한다.
- 모든 재사용 역할은 `.claude/agents/{name}.md`에 정의한다.
- 스킬은 `.claude/skills/{name}/SKILL.md`에 둔다.

## Delegation Mapping

| Core 개념 | Claude Code 구현 |
|---|---|
| Specialist | `.claude/agents/{name}.md` + `Agent` 또는 Team member |
| Parallel fan-out | `TeamCreate` + `TaskCreate`, 또는 background `Agent` |
| Reviewer | 팀원 또는 별도 `Agent` |
| Direct communication | `SendMessage` |
| Shared task state | `TaskCreate` / `TaskUpdate` |
| Shared workspace | `_workspace/{phase}_{role}_{artifact}.md` |

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
1. 세션 시작 시 `context/` 아래 Markdown 파일을 확인한다.
2. 작업 전 `BRIEF.md`와 `context/WORKING-RULES.md`를 읽는다.
3. 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`를 갱신한다.
```
