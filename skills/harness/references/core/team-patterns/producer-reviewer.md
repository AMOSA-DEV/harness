# Producer-Reviewer

Producer-Reviewer는 생성 역할과 검증 역할을 분리해 품질을 통제하는 팀 구조다.

```text
[생성] -> [검증] -> [수정 또는 재생성]
```

## 적합한 경우

- 산출물 품질 기준을 명확히 쓸 수 있다.
- 독립 검토가 실제로 품질을 올린다.
- 재작업 루프를 허용할 수 있지만 횟수를 제한해야 한다.

## 실행 형태

- 기본은 격리된 역할 호출 또는 협업 팀이다.
- 기준이 명확하고 단순 판정이면 격리된 역할 호출로 충분하다.
- 생성자와 검증자가 기준을 조율해야 하거나 재작업이 잦으면 협업 팀이 유용하다.

## 피할 때

- 검증 기준이 모호하면 먼저 기준을 정의한다.
- 여러 독립 관점의 검토가 필요하면 Fan-out/Fan-in과 결합한다.
- 작업이 단순하고 리뷰가 형식적이면 단일 역할로 충분할 수 있다.

## 역할 구성

| 역할 | 책임 | 출력 |
|---|---|---|
| producer | 초안, 구현, 산출물을 만든다 | `_workspace/machine/02_draft.md` |
| reviewer | 기준에 따라 PASS, FIX, REDO를 판단한다 | `_workspace/audit/03_review.md` |
| producer | 필요한 수정 또는 재생성을 수행한다 | `_workspace/machine/04_revision.md` |

## 오케스트레이션

1. 생성 전에 검증 기준과 최대 재시도 횟수를 정한다.
2. producer가 초안을 만든다.
3. reviewer가 `PASS`, `FIX`, `REDO` 중 하나로 판정한다.
4. `FIX`면 구체 수정 목록을 producer에게 돌려보낸다.
5. `REDO`면 정해진 횟수 안에서 다시 생성한다.
6. 최종 결과에는 남은 이슈와 재시도 횟수를 기록한다.

## 검증 판정 형식

reviewer는 판정을 구조화해 남긴다:

```markdown
## Review
- 판정: PASS | FIX | REDO
- 사유: [구체적 이유]
- 수정 지시: [FIX/REDO인 경우 구체적 수정 방향]
- 재검증 필요 여부: yes | no
```

부분 산출물이 여러 개면 항목별로 같은 형식을 반복한다.

## 재시도 정책

```yaml
max_retries: 2
retry_trigger: REDO
fallback: "최대 재시도 후 unresolved quality issues로 보고"
```

재시도 횟수는 기본값이며, 품질 기준이나 비용이 다르면 하네스별로 조정할 수 있다.

## 실패 처리

| 상황 | 대응 |
|---|---|
| reviewer가 수정 가능한 문제를 발견 | 구체적인 fix list를 producer에게 전달한다 |
| REDO가 반복됨 | 최대 횟수 후 중단하고 unresolved issue로 보고한다 |
| 기준 충돌 | 사용자 확인 또는 임시 rubric을 만든다 |
| producer가 지시를 이해하지 못함 | 예시와 통과 기준을 포함해 재지시한다 |

## 점검 질문

- reviewer가 사용할 기준이 생성 전에 정의됐는가?
- 재시도 횟수와 중단 조건이 명확한가?
- reviewer가 producer의 취향 평가자가 아니라 기준 평가자인가?
- 최종 결과에 남은 품질 이슈가 명시되는가?
