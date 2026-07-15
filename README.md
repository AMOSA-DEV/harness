# Context-Harness

**English** | [한국어](README_KO.md)

> Sets up and maintains the minimum durable project context needed for long-running AI collaboration.

## Features

- **Context first** — organizes project knowledge and working rules before adding automation
- **Minimal structure** — creates only `AGENTS.md`, `ROADMAP.md`, and `context/` by default
- **Preserves existing systems** — does not force migrations from established instructions or output folders
- **Parallel-task aware** — shared files have a single coordination path while task state stays isolated
- **Workspace by consent** — creates `_workspace/` only after user approval

## Default structure

```text
AGENTS.md
ROADMAP.md
context/
├── ABOUT-ME.md
├── BRAND-VOICE.md
├── WORKING-RULES.md
├── GLOSSARY.md
└── LESSONS.md
```

When approved, the project also gets:

```text
_workspace/
├── audit/
├── human/
└── machine/
    └── tasks/
```

`BRIEF.md` and `HANDOFF.md` are not created by default. The active conversation carries task context, while per-task files carry parallel task status.

## Installation

### Codex

```bash
mkdir -p ~/.codex/skills
cp -R harness/skills/harness ~/.codex/skills/
```

### Claude Code

```bash
mkdir -p ~/.claude/skills
cp -R harness/skills/harness ~/.claude/skills/
```

## Usage

```text
Set up a minimal harness for this project
Organize AGENTS.md and the project context
Simplify the existing harness
Audit the current context structure
```

Agent, skill, and orchestrator generation are outside this skill's default scope.

## License

Apache 2.0 — Based on the original [Harness](https://github.com/revfactory/harness) by robin.
