# Context-Harness

**English** | [한국어](README_KO.md)

> Context-based team architecture factory for Claude Code. Extends [Harness](reference/harness/) with automated project context initialization and session management.

## Features

- **Automated Context Generation** — Calling `/harness` auto-creates `context/`, `outputs/`, `BRIEF.md`, `HANDOFF.md`, `ROADMAP.md`, `CLAUDE.md`
- **Agent Team Design** — 6 architecture patterns
- **Skill Generation** — Efficient context management via Progressive Disclosure
- **Workspace Separation** — Clean git history by separating source (`skills/`) from runtime (`_workspace/`)

## Installation

> **Private repo.** Direct installation only. Marketplace is not supported.

### Method 1: SSH Clone (Recommended)

```bash
git clone git@github.com:AMOSA-DEV/harness.git
cp -r harness/skills/harness ~/.claude/skills/harness
```

### Method 2: HTTPS Clone with PAT

```bash
# Create a PAT with 'repo' scope at https://github.com/settings/tokens
git clone https://<TOKEN>@github.com/AMOSA-DEV/harness.git
cp -r harness/skills/harness ~/.claude/skills/harness
```

### Method 3: Direct Download

```bash
# Download ZIP from GitHub, extract, then:
cp -r harness/skills/harness ~/.claude/skills/harness
```

## Usage

```
Build a harness for this project
하네스 구성해줘
```

## Architecture Patterns

### 1. Pipeline
Sequential dependent tasks. The output of the previous agent becomes the input of the next.

```
[Analysis] → [Design] → [Implementation] → [Verification]
```

**Best for:** Strong sequential dependencies between steps
**Example:** Novel writing — Worldbuilding → Characters → Plot → Draft → Editing

### 2. Fan-out/Fan-in
Parallel independent tasks with result integration.

```
         ┌→ [Expert A] ─┐
[Input] ─┼→ [Expert B] ─┼→ [Integration]
         └→ [Expert C] ─┘
```

**Best for:** Multiple perspectives needed for the same input
**Example:** Comprehensive research — Official/Media/Community/Background parallel investigation → Integrated report

### 3. Expert Pool
Selectively invoke the appropriate expert based on context.

```
[Router] → { Expert A | Expert B | Expert C }
```

**Best for:** Different processing needed depending on input type
**Example:** Code review — Only call the relevant domain expert (Security/Performance/Architecture)

### 4. Producer-Reviewer
Generation and quality verification work as a pair.

```
[Generate] → [Review] → (if issues) → [Generate] retry
```

**Best for:** Output quality assurance is important and objective criteria exist
**Example:** Webtoon — Artist generates → Reviewer checks → Regenerate problematic panels

### 5. Supervisor
A central agent manages task status and dynamically distributes work.

```
         ┌→ [Worker A]
[Supervisor] ─┼→ [Worker B]    ← Supervisor monitors and adjusts
         └→ [Worker C]
```

**Best for:** Variable workload or runtime distribution decisions needed
**Example:** Large-scale code migration — Supervisor analyzes file list and assigns workers

### 6. Hierarchical Delegation
Top agents recursively delegate to subordinate agents.

```
[Lead] → [Manager A] → [Staff A1]
                  → [Staff A2]
       → [Manager B] → [Staff B1]
```

**Best for:** Problems that naturally decompose hierarchically
**Example:** Full-stack app — Lead → Frontend Manager → (UI/Logic/Testing) + Backend Manager → (API/DB/Testing)

## Project Structure

```
context-harness/
├── skills/harness/          # Source (git tracked)
│   ├── SKILL.md
│   ├── references/
│   └── team-architecture-templates/
├── _workspace/              # Runtime outputs (git ignored)
├── reference/harness/       # Original Harness (git ignored)
├── LICENSE
├── NOTICE
└── README.md
```

## License

Apache 2.0 — Based on the original [Harness](https://github.com/revfactory/harness) by robin.
