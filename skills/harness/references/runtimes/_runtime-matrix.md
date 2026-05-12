# Runtime Matrix

런타임은 “AI 제공사”가 아니라 “하네스가 실제로 실행되는 에이전트 환경”이다.

| Concern | Codex | Claude Code |
|---|---|---|
| Pointer file | `AGENTS.md` | `CLAUDE.md` |
| Project skills | `.codex/skills/{name}/SKILL.md` | `.claude/skills/{name}/SKILL.md` |
| Global skills | `~/.codex/skills/{name}/SKILL.md` | `~/.claude/skills/{name}/SKILL.md` |
| Agent specs | `.codex/agents/{name}.md` | `.claude/agents/{name}.md` |
| Delegation | `spawn_agent`, `send_input`, `wait_agent`, `close_agent` when available and allowed | `TeamCreate`, `SendMessage`, `TaskCreate`, `TaskUpdate`, `Agent` when available |
| Direct team communication | Not guaranteed | Supported in Agent Teams environments |
| Default exchange | File-based `_workspace/` plus returned summaries | File-based `_workspace/` plus team messages/tasks |
| Model names | GPT/Codex model names, often inherited from session | `haiku`, `sonnet`, `opus` |
| Manual edits | `apply_patch` preferred | Claude Code edit/write tools |
| Packaging | `.codex-plugin/plugin.json` when packaging as plugin | `.claude-plugin/plugin.json` when packaging as plugin |

## Detection

Use explicit user intent first. If the user says “Codex용”, choose Codex. If the user says “Claude Code용”, choose Claude Code.

If user intent is absent, infer from the active environment:

| Signal | Runtime |
|---|---|
| `AGENTS.md`, `.codex/`, `~/.codex/skills`, Codex tools | Codex |
| `CLAUDE.md`, `.claude/`, `~/.claude/skills`, Claude Code tools | Claude Code |

If both are present, ask only when the target is ambiguous and generating the wrong files would be costly. Otherwise generate the active runtime first and note how to add the other runtime later.
