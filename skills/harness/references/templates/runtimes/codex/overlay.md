# Codex Template Overlay

Apply this overlay after selecting a core architecture template.

## General Rules

- Treat `.codex/agents/{name}.md` files as role specs, not guaranteed auto-registered subagent types.
- Use file-based exchange through `_workspace/` as the default.
- Use `spawn_agent` only when available and when the user has explicitly asked for delegation, subagents, or parallel agent work.
- If delegation is not available, execute the same pattern sequentially in the main session.

## Pattern Mapping

| Core pattern | Codex implementation |
|---|---|
| Pipeline | Main session or one worker at a time; pass prior file output into the next prompt |
| Fan-out/Fan-in | Parallel `spawn_agent` workers when allowed; otherwise sequential isolated passes |
| Expert Pool | Main session routes, then spawns only selected workers when allowed |
| Producer-Reviewer | Producer worker + reviewer worker, or main-session two-pass loop |
| Supervisor | Main session acts as supervisor; workers own disjoint batches |
| Hierarchical Delegation | Prefer flattening; avoid nested agent trees unless explicitly supported |

## Prompt Requirements

When spawning a Codex worker, include:

1. The role spec summary
2. Exact files it may read
3. Exact files or responsibility it owns
4. Output path under `_workspace/`
5. Reminder that other agents may be working in the same codebase
6. Instruction not to revert unrelated edits
