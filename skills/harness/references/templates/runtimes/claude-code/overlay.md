# Claude Code Template Overlay

Apply this overlay after selecting a core architecture template.

## General Rules

- Reusable roles live in `.claude/agents/{name}.md`.
- Skills live in `.claude/skills/{name}/SKILL.md`.
- Prefer Agent Teams when direct coordination materially improves quality.
- If Agent Teams are unavailable, fall back to file-based sequential execution or background `Agent` calls.

## Pattern Mapping

| Core pattern | Claude Code implementation |
|---|---|
| Pipeline | Sequential `Agent` calls or a single orchestrator pass |
| Fan-out/Fan-in | `TeamCreate` plus task allocation and team messages when available |
| Expert Pool | Router plus selected `Agent` calls |
| Producer-Reviewer | Team pair or bounded `Agent` loop |
| Supervisor | `TaskCreate`/`TaskUpdate` when available; otherwise supervisor file ledger |
| Hierarchical Delegation | Flatten where possible; avoid deep nested teams |

## Team Communication

Use direct messages only for information that changes another role's work. Durable findings still go into `_workspace/` files.
