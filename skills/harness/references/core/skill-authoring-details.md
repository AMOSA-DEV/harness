# Skill Authoring Details

이 문서는 `core/skill-authoring-overview.md`의 상세 보충이다. 런타임 전용 트리거 성향은 각 runtime adapter에 둔다.

## Description

description에는 다음을 포함한다:

1. 스킬이 수행하는 작업
2. 구체적 트리거 상황
3. near-miss 경계 조건
4. 후속 작업 표현: 재실행, 업데이트, 수정, 보완, 이전 결과 개선

좋은 description은 모호한 명사보다 작업 동사를 나열한다.

```yaml
description: "PDF 파일 읽기, 텍스트/테이블 추출, 병합, 분할, 회전, 워터마크, OCR 등 PDF 작업을 수행. .pdf 파일을 언급하거나 PDF 산출물을 요청하면 사용한다. 단순 요약이 아니라 변환/편집/분석이 필요할 때 특히 적합."
```

런타임별로 description을 고르는 방식은 다를 수 있다. 그래도 공통 원칙은 같다: 스킬이 맡는 행동, 트리거해야 하는 표현, 트리거하지 말아야 하는 near-miss, 후속 실행 표현을 한 번에 드러낸다.

## Body Style

- Why-first: 규칙만 쓰지 말고 이유를 짧게 설명한다.
- 일반화: 테스트 피드백은 특정 예시 패치가 아니라 원리로 반영한다.
- 명령형: 스킬은 지시서이므로 “한다/하라”를 사용한다.
- 컨텍스트 절약: 없어도 모델이 잘하는 일반 지식은 제거한다.

## Output Format

산출물 형식이 중요한 스킬은 템플릿을 명시한다.

```markdown
## 보고서 구조
# [제목]
## 요약
## 핵심 발견
## 권장 사항
```

## Progressive Disclosure Patterns

| 패턴 | 사용 시점 |
|---|---|
| 도메인별 reference 분리 | 한 스킬이 여러 도메인을 지원할 때 |
| 조건부 상세 reference | 특정 기능에서만 긴 지식이 필요할 때 |
| 대형 reference 목차 | reference가 300줄 이상일 때 |

## Script Bundling Signals

| 신호 | 조치 |
|---|---|
| 테스트마다 같은 helper script 작성 | `scripts/`에 번들링 |
| 매번 같은 설치/초기화 반복 | 표준 절차로 문서화 |
| 같은 에러 후 같은 회피책 반복 | known issue와 해결법 추가 |

번들링한 script는 실행 방법, 입력, 출력, 실패 시 행동을 스킬 본문이나 가까운 reference에 적는다. 스크립트가 런타임 전용 도구나 경로에 의존하면 해당 runtime adapter에만 남긴다.

## Data Schemas

### eval_metadata.json

```json
{
  "eval_id": 0,
  "eval_name": "descriptive-name",
  "prompt": "사용자의 작업 프롬프트",
  "assertions": [
    "산출물에 X가 포함되어 있다",
    "Y 형식으로 파일이 생성되었다"
  ]
}
```

### grading.json

```json
{
  "expectations": [
    {
      "text": "산출물에 '서울'이 포함됨",
      "passed": true,
      "evidence": "3번째 단계에서 확인"
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

필드명은 `text`, `passed`, `evidence`를 사용한다.

### timing.json

```json
{
  "total_tokens": 84852,
  "duration_ms": 23332,
  "total_duration_seconds": 23.3
}
```

## Verification Agent Rules

검증 역할은 `PASS`, `FIX`, `REJECT` 판정을 사용한다.

| 판정 | 의미 | 후속 조치 |
|---|---|---|
| PASS | 품질 기준 충족 | 종료 |
| FIX | 부분 수정 필요 | 구체적 수정 지시 후 재검증 |
| REJECT | 기준 미달 | 사용자 보고 또는 재작성 |

FIX 지시는 “수정하라”가 아니라 파일/섹션/변경 내용을 구체적으로 적는다. 재시도는 기본 2회로 제한한다.

FIX 판정 후에는 실제 변경이 있었는지 확인한다. 가능한 경우 diff, 파일 해시, 또는 다시 읽은 내용으로 수정 전후를 비교한다. “수정했다”는 보고만 있고 파일 변화가 없으면 검증 실패로 본다.

PASS는 “수정 사항이 없음”이 아니라 품질 기준을 충족했다는 뜻이다. 검증 기준이 모호하면 먼저 rubric을 만든 뒤 판정한다.
