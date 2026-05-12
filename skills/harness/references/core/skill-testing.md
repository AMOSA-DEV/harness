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

## Assertions

좋은 assertion:

- 객관적으로 참/거짓 판별 가능
- 스킬의 핵심 가치를 검증
- 결과만 봐도 무엇을 검사하는지 명확

나쁜 assertion:

- 스킬 유무와 무관하게 항상 통과
- “잘 작성되었다”처럼 주관적

두 구성 모두 항상 통과하는 non-discriminating assertion은 제거하거나 더 어렵게 만든다.

## Specialist Roles

| 역할 | 책임 |
|---|---|
| Grader | assertion별 통과/실패와 근거 작성 |
| Comparator | A/B 결과를 블라인드 비교 |
| Analyzer | 고분산 eval, 쉬운 assertion, 비용 대비 품질 분석 |

## Iteration Loop

1. 스킬 수정
2. 새 iteration 디렉토리에 테스트 재실행
3. 이전 iteration과 비교
4. 피드백을 일반화해 반영
5. 의미 있는 개선이 없거나 사용자가 만족하면 종료

## Trigger Eval

description 검증은 should-trigger 8~10개와 should-not-trigger 8~10개로 구성한다.

near-miss가 중요하다. 키워드는 비슷하지만 다른 스킬이나 직접 답변이 적합한 쿼리를 포함한다.

고급 최적화가 필요하면 train/test split을 사용하고, train에 과적합하지 않는다.
