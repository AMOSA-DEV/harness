# Claude Code Orchestrator Overlay

Use this with `references/templates/core/orchestrator.md`.

## Execution Modes

| Core need | Claude Code behavior |
|---|---|
| Direct team coordination | Use Agent Teams when available |
| Parallel specialists | Use `TeamCreate` + task allocation, or background `Agent` fallback |
| Role prompt | Use `.claude/agents/{role}.md` for reusable roles |
| Shared updates | Use `TaskCreate`/`TaskUpdate` when available |
| Direct role communication | Use `SendMessage` only for information that changes another role's work |

## Team Mode Skeleton

```text
1. Create team with selected members.
2. Register tasks with owners and dependencies.
3. Members write durable outputs to `_workspace/`.
4. Leader monitors progress.
5. Leader reads outputs and integrates.
6. Delete or close team when finished.
```

## Agent Fallback Skeleton

```text
1. Call selected agents directly.
2. Use background execution only for independent work.
3. Collect returned summaries and `_workspace/` files.
4. Integrate in the main session.
```

## Claude Code-Specific Validation

- Team mode instructions may mention `TeamCreate`, `SendMessage`, `TaskCreate`, and `TaskUpdate`.
- Subagent mode instructions may mention `Agent`, `subagent_type`, `run_in_background`, and model names.
- Keep durable findings in `_workspace/` even when team messages are used.
