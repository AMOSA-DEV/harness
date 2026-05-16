# Skill Testing

스킬 품질 검증은 정성 평가와 정량 평가를 함께 사용한다.

## Test Prompt Design

테스트 프롬프트는 실제 사용자가 입력할 법해야 한다.

- 공식적/캐주얼 톤 혼합
- 명시적/암시적 의도 혼합
- 단순/복합 작업 혼합
- 약어, 오타, 개인적 맥락 포함

최소 세트:

1. 핵심 사용 사례
2. 엣지 케이스
3. 복합 작업

## With-Skill vs Baseline

각 테스트는 같은 프롬프트로 비교한다.

| 대상 | 설명 |
|---|---|
| with_skill | 새 스킬을 읽고 수행 |
| baseline | 스킬 없음 또는 수정 전 스킬 |

결과는 `_workspace/iteration-{n}/eval-{name}/` 아래에 보존한다.

Baseline 선택:

| 상황 | Baseline |
|---|---|
| 새 스킬 생성 | 스킬 없이 같은 프롬프트 실행 |
| 기존 스킬 개선 | 수정 전 스킬 버전 또는 이전 iteration 산출물 |

가능하면 with_skill과 baseline을 같은 입력, 같은 제한, 같은 산출물 형식으로 실행한다. 실행 시간이 길거나 비용이 큰 경우에는 대표 프롬프트부터 비교한다.

타이밍이나 토큰 데이터가 런타임 알림에서만 보이면 즉시 저장한다. 나중에 복구할 수 없을 수 있다.

병렬 subagent 실행이 허용되지 않는 런타임에서는 같은 조건을 유지한 순차 실행으로 대체한다. 이 경우 순서 효과가 생길 수 있으므로 실행 순서와 사용한 스냅샷을 기록한다.

## Assertions

좋은 assertion:

- 객관적으로 참/거짓 판별 가능
- 스킬의 핵심 가치를 검증
- 결과만 봐도 무엇을 검사하는지 명확

나쁜 assertion:

- 스킬 유무와 무관하게 항상 통과
- “잘 작성되었다”처럼 주관적

두 구성 모두 항상 통과하는 non-discriminating assertion은 제거하거나 더 어렵게 만든다.

Assertion이 코드로 검증 가능하면 반복 가능한 스크립트나 체크리스트로 만든다. 주관 품질은 rubric을 먼저 만든 뒤 비교한다.

채점 결과는 다음 형식을 권장한다:

```json
{
  "expectations": [
    {
      "text": "핵심 산출물이 지정 경로에 생성됨",
      "passed": true,
      "evidence": "outputs/report.md 확인"
    }
  ],
  "summary": {
    "passed": 1,
    "failed": 0,
    "total": 1,
    "pass_rate": 1.0
  }
}
```

## Specialist Roles

| 역할 | 책임 |
|---|---|
| Grader | assertion별 통과/실패와 근거 작성 |
| Comparator | A/B 결과를 블라인드 비교 |
| Analyzer | 고분산 eval, 쉬운 assertion, 비용 대비 품질 분석 |

Comparator를 쓸 때는 산출물을 A/B로 익명화한다. 어떤 결과가 with_skill인지 알려주면 판정이 흔들릴 수 있다.

## Iteration Loop

1. 스킬 수정
2. 새 iteration 디렉토리에 테스트 재실행
3. 이전 iteration과 비교
4. 피드백을 일반화해 반영
5. 의미 있는 개선이 없거나 사용자가 만족하면 종료

개선 원칙:

- 테스트 예시에만 맞춘 좁은 패치는 피한다.
- 불필요하게 긴 규칙은 제거하거나 references로 옮긴다.
- 반복 생성되는 helper code나 절차는 scripts 또는 표준 절차로 번들링한다.
- 초안을 쓴 뒤 새로운 시각으로 다시 읽고, 모호한 지시를 줄인다.

## Trigger Eval

description 검증은 should-trigger 8~10개와 should-not-trigger 8~10개로 구성한다.

near-miss가 중요하다. 키워드는 비슷하지만 다른 스킬이나 직접 답변이 적합한 쿼리를 포함한다.

고급 최적화가 필요하면 train/test split을 사용하고, train에 과적합하지 않는다.

기존 스킬과의 충돌도 확인한다:

1. 기존 스킬 description을 수집한다.
2. 새 스킬의 should-trigger 쿼리가 기존 스킬로 잘못 빨려 들어가지 않는지 본다.
3. should-not-trigger 쿼리가 새 스킬을 트리거하면 description에 경계 조건을 추가한다.

트리거 쿼리는 공식적 표현과 캐주얼 표현, 명시적 요청과 암시적 요청, 파일명/도메인명이 들어간 현실적인 표현을 섞는다. “피보나치 함수 작성”처럼 명백히 무관한 예시는 near-miss가 아니므로 가치가 낮다.

## Minimal Test Set

시간이 부족해 전체 실행 테스트를 못 하면 다음 최소 세트를 남긴다:

1. 대표 should-trigger 3개와 should-not-trigger 3개
2. 정상 흐름 드라이런 1개
3. 실패 또는 부분 재실행 드라이런 1개
4. 미검증 범위와 생략 사유

하네스를 실제 프로젝트에 설치하기 전에는 전체 trigger eval과 최소 1개 실행 또는 드라이런을 완료해야 한다.

## Workspace Layout

```text
_workspace/skill-evals/{skill-name}/
├── iteration-1/
│   ├── eval-{descriptive-name}/
│   │   ├── eval_metadata.json
│   │   ├── with_skill/
│   │   │   ├── outputs/
│   │   │   ├── timing.json
│   │   │   └── grading.json
│   │   └── baseline/
│   │       ├── outputs/
│   │       ├── timing.json
│   │       └── grading.json
│   └── benchmark.json
└── iteration-2/
```

디렉토리는 숫자만 쓰지 말고 `eval-multi-page-table`처럼 무엇을 검증하는지 알 수 있게 짓는다. 이전 iteration은 덮어쓰지 않는다.
