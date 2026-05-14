# Context-Harness

**English** | [한국어](README_KO.md)

> Runtime-aware team architecture factory. Turns a project brief into agent roles, skills, orchestration guidance, and durable context for Codex and Claude Code.

## Features

- **Runtime-aware generation** — separates common harness design from Codex/Claude Code implementation details
- **Automated context setup** — creates `context/`, `BRIEF.md`, `HANDOFF.md`, and `ROADMAP.md`
- **Agent/team architecture design** — supports 6 reusable patterns
- **Skill generation** — uses Progressive Disclosure to keep context lean
- **Workspace separation** — keeps source (`skills/`) separate from runtime outputs (`_workspace/`)

## Installation

> **Private repo.** Direct installation only. Marketplace packaging is not the primary path yet.

### Codex

```bash
git clone git@github.com:AMOSA-DEV/harness.git
cp -r harness/skills/harness ~/.codex/skills/harness
```

### Claude Code

```bash
git clone git@github.com:AMOSA-DEV/harness.git
cp -r harness/skills/harness ~/.claude/skills/harness
```

If you download the ZIP instead of cloning, extract it first and copy the same `skills/harness` directory to your runtime's global skills folder.

## Usage

```text
Build a harness for this project
Build a Codex harness for this project
Build a Claude Code harness for this project
하네스 구성해줘
Codex용 하네스 구성해줘
```

## Runtime Model

Harness is split into two layers:

```text
Common Core: what the harness should design
Runtime Adapter: how a specific AI runtime represents and executes it
```

Current runtime adapters:

| Runtime | Pointer file | Skills | Agent specs |
|---|---|---|---|
| Codex | `AGENTS.md` | `.codex/skills/` | `.codex/agents/` |
| Claude Code | `CLAUDE.md` | `.claude/skills/` | `.claude/agents/` |

## Architecture Patterns

1. **Pipeline** — sequential dependent tasks
2. **Fan-out/Fan-in** — parallel independent work followed by integration
3. **Expert Pool** — route to the right expert by input type
4. **Producer-Reviewer** — generate, verify, revise
5. **Supervisor** — central coordination with dynamic assignment
6. **Hierarchical Delegation** — nested decomposition for naturally layered work

## Project Structure

```text
context-harness/
├── skills/harness/
│   ├── SKILL.md
│   ├── references/
│   │   ├── core/
│   │   ├── runtimes/
│   │   ├── templates/
│   │   └── examples/
├── _workspace/
├── LICENSE
├── NOTICE
└── README.md
```

## License

Apache 2.0 — Based on the original [Harness](https://github.com/revfactory/harness) by robin.
