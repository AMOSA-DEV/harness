# Context-Harness

[English](README.md) | **한국어**

> 프로젝트가 AI와 오래 협업하는 데 필요한 최소 컨텍스트 구조를 구성하고 정리합니다.

## 특징

- **컨텍스트 우선** — 에이전트·스킬보다 프로젝트 지식과 작업 규칙을 먼저 정리
- **최소 구조** — `AGENTS.md`, `ROADMAP.md`, `context/`만 기본 생성
- **기존 구조 보존** — 이미 있는 지침과 산출물 체계를 강제 이전하지 않음
- **병렬 작업 대응** — 공통 파일은 조정 작업만 갱신하고 개별 작업 상태는 분리
- **승인형 Workspace** — `_workspace/`는 사용자 승인 후에만 생성

## 기본 구조

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

승인한 프로젝트에서는 다음 구조를 추가합니다.

```text
_workspace/
├── audit/
├── human/
└── machine/
    └── tasks/
```

`BRIEF.md`와 `HANDOFF.md`는 기본 생성하지 않습니다. 현재 목표와 실행 과정은 대화가 관리하고, 병렬 작업 상태는 작업별 task 파일이 담당합니다.

## 설치

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

## 사용 예시

```text
이 프로젝트에 최소 하네스를 구성해줘
AGENTS.md와 프로젝트 컨텍스트를 정리해줘
기존 하네스를 담백하게 정리해줘
현재 컨텍스트 구조를 감사해줘
```

에이전트, 스킬, 오케스트레이터 생성은 이 스킬의 기본 범위가 아닙니다.

## 라이선스

Apache 2.0 — 원작자 [robin](https://github.com/revfactory/harness)의 Harness를 기반으로 합니다.
