# Context-Harness

[English](README.md) | **한국어**

> 런타임 인식 팀 아키텍처 팩토리. 프로젝트 설명을 Codex와 Claude Code에 맞는 에이전트 역할, 스킬, 오케스트레이션, 지속 컨텍스트로 변환합니다.

## 특징

- **런타임 인식 생성** — 공통 하네스 설계와 Codex/Claude Code 구현 차이를 분리
- **컨텍스트 자동 생성** — `context/`, `BRIEF.md`, `HANDOFF.md`, `ROADMAP.md` 생성
- **에이전트/팀 아키텍처 설계** — 6가지 재사용 패턴 지원
- **스킬 생성** — Progressive Disclosure로 컨텍스트를 가볍게 유지
- **워크스페이스 분리** — 소스(`skills/`)와 런타임 산출물(`_workspace/`) 분리

## 설치

> **Private repo입니다.** 직접 설치만 우선 지원합니다. Marketplace 패키징은 아직 주 경로가 아닙니다.

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

ZIP으로 받았다면 압축을 푼 뒤 같은 `skills/harness` 디렉토리를 사용하는 런타임의 글로벌 스킬 폴더로 복사하면 됩니다.

## 사용법

```text
하네스 구성해줘
Codex용 하네스 구성해줘
Claude Code용 하네스 구성해줘
Build a harness for this project
```

## 런타임 모델

Harness는 두 층으로 나뉩니다.

```text
Common Core: 하네스가 무엇을 설계해야 하는가
Runtime Adapter: 특정 AI 런타임에서 어떻게 표현하고 실행하는가
```

현재 런타임 어댑터:

| Runtime | Pointer file | Skills | Agent specs |
|---|---|---|---|
| Codex | `AGENTS.md` | `.codex/skills/` | `.codex/agents/` |
| Claude Code | `CLAUDE.md` | `.claude/skills/` | `.claude/agents/` |

## 아키텍처 패턴

1. **파이프라인** — 순차 의존 작업
2. **팬아웃/팬인** — 병렬 작업 후 통합
3. **전문가 풀** — 입력 유형에 따라 적절한 전문가 선택
4. **생성-검증** — 생성, 검증, 수정
5. **감독자** — 중앙 조율과 동적 배정
6. **계층적 위임** — 계층적으로 자연스럽게 나뉘는 작업

## 프로젝트 구조

```text
context-harness/
├── skills/harness/
│   ├── SKILL.md
│   ├── references/
│   │   ├── core/
│   │   ├── runtimes/
│   │   ├── context/
│   │   ├── templates/
│   │   ├── examples/
│   │   └── meta/
├── _workspace/
├── LICENSE
├── NOTICE
└── README.md
```

## 라이선스

Apache 2.0 — 원작자 [robin](https://github.com/revfactory/harness)의 Harness를 기반으로 합니다.
