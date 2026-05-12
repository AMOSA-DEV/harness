# Codex Orchestrator Overlay

Use this with `references/templates/core/orchestrator.md`.

## Execution Modes

| Core need | Codex behavior |
|---|---|
| Sequential execution | Main session executes phases directly |
| Parallel specialists | Use `spawn_agent` only when user explicitly requested subagents/delegation/parallel agent work |
| Role prompt | Paste or summarize `.codex/agents/{role}.md`; do not assume it auto-registers |
| Waiting | Use `wait_agent` sparingly only when result blocks next step |
| Follow-up | Use `send_input` for existing spawned agents only when still relevant |

## Worker Prompt Requirements

When spawning a worker, include:

1. It is not alone in the codebase.
2. Its owned files or responsibility.
3. Files it should read.
4. Output path under `_workspace/`.
5. It must not revert unrelated edits.
6. It should list changed files in its final response when editing.

## Main Session Fallback

If delegation is unavailable or not requested:

1. Execute the role sequence in the main session.
2. Keep each role's output in a separate `_workspace/` file.
3. Preserve the role names in headings so later migration to subagents is easy.

## Codex-Specific Validation

- Do not generate instructions requiring Claude Code tools.
- Do not assume nested delegation is available.
- Prefer `apply_patch` for manual edits.
- Follow `AGENTS.md` and local workspace instructions.
