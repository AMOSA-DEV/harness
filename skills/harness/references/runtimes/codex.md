# Codex Runtime Adapter

이 문서는 Codex에서 하네스를 구현할 때만 읽는다.

## Output Paths

| 산출물 | 위치 |
|---|---|
| Pointer file | `AGENTS.md` |
| Project skill | `.codex/skills/{skill-name}/SKILL.md` |
| Agent spec | `.codex/agents/{agent-name}.md` |
| Orchestrator skill | `.codex/skills/{orchestrator-name}/SKILL.md` |
| Runtime workspace | `_workspace/` |

## Codex 제약

- `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`, `Agent(subagent_type)`를 Codex 산출물에 요구하지 않는다.
- `.codex/agents/{name}.md`는 재사용 가능한 역할 스펙이지, 자동 등록되는 내장 subagent 타입이라고 가정하지 않는다.
- subagent/parallel 작업은 사용자가 명시적으로 요청했거나 현재 런타임 정책상 허용될 때만 사용한다.
- 파일 편집 지침은 `apply_patch`와 기존 코드베이스 패턴을 우선한다.
- 보안 정보, 환경변수, API 키, 인증 정보 작업은 금지한다.

## Delegation Mapping

| Core 개념 | Codex 구현 |
|---|---|
| Specialist | `spawn_agent` worker/explorer에 역할 스펙을 요약해 전달 |
| Parallel fan-out | 여러 `spawn_agent`를 병렬로 실행, 결과는 파일/최종 메시지로 수집 |
| Reviewer | 별도 worker/explorer 또는 메인 세션의 독립 검토 단계 |
| Shared workspace | `_workspace/{phase}_{role}_{artifact}.md` |
| Handoff | `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md` 갱신 |

Codex에서 delegation이 불가능하거나 요청되지 않았으면, 오케스트레이터는 메인 세션에서 같은 단계를 순차 실행한다.

## Agent Spec Template

```markdown
---
name: {agent-name}
description: "{역할과 트리거를 1-2문장으로 설명}"
---

# {Agent Name}

## 역할
{핵심 책임}

## 입력
{읽을 파일, 사용자 입력, 이전 단계 산출물}

## 출력
{쓸 파일과 형식}

## 작업 원칙
- {원칙}

## Codex 실행 지침
- 이 파일은 역할 스펙이다. subagent로 실행할 때는 이 내용을 프롬프트에 요약해 전달한다.
- 병렬 실행이 허용되지 않으면 메인 세션에서 순차 실행한다.
- 변경 전 관련 파일을 읽고, 수동 편집은 apply_patch를 우선한다.
```

## AGENTS.md Pointer Template

```markdown
## Harness: {domain}

목표: {목표 한 줄}

트리거: {도메인} 관련 다단계 작업, 하네스 실행, 하네스 수정/점검 요청 시 `{orchestrator-skill-name}` 스킬을 사용한다.

## Context 참조 규칙
1. 세션 시작 시 `context/` 아래 Markdown 파일을 확인한다.
2. 작업 전 `BRIEF.md`와 `context/WORKING-RULES.md`를 읽는다.
3. 완료 후 `HANDOFF.md`, `ROADMAP.md`, `context/LESSONS.md`를 갱신한다.
4. 보안 정보, API 키, 인증 정보 작업은 하지 않는다.
```
