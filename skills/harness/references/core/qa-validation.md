# QA Validation

QA 역할은 “존재 확인”보다 “경계면 교차 비교”를 우선한다.

## Boundary Mismatch

두 컴포넌트가 각각 올바르게 보여도 연결 지점에서 계약이 어긋나는 결함이다.

| 경계면 | 불일치 예시 |
|---|---|
| API 응답 -> 프론트 훅 | API가 `{ projects: [...] }` 반환, 훅이 배열 기대 |
| API 필드명 -> 타입 정의 | API는 `thumbnailUrl`, 타입은 `thumbnail_url` |
| 파일 경로 -> 링크 | 페이지는 `/dashboard/create`, 링크는 `/create` |
| 상태 전이 맵 -> 업데이트 코드 | 맵에는 전이 정의, 실제 업데이트 누락 |
| 즉시 응답 -> 비동기 결과 | 즉시 `{ status }` 반환인데 프론트가 최종 결과 필드 접근 |

## Core Principle

QA는 한쪽만 읽지 않는다. 반드시 생산자와 소비자를 동시에 읽고 비교한다.

| 검증 대상 | 생산자 | 소비자 |
|---|---|---|
| API 응답 shape | route/controller 응답 | hook/client 타입 |
| 라우팅 | page 파일 경로 | href/router/redirect |
| 상태 전이 | 상태 맵 | 실제 status 업데이트 |
| DB -> API -> UI | DB 컬럼/모델 | API 응답/프론트 타입 |

## Web App Checklist

### API and Frontend

- [ ] API 응답 shape과 대응 훅/클라이언트 타입이 일치
- [ ] 래핑된 응답은 소비자에서 unwrap
- [ ] snake_case/camelCase 변환 일관
- [ ] 즉시 응답과 최종 비동기 결과 구분
- [ ] API 엔드포인트와 프론트 호출 경로 매핑

### Routing

- [ ] href/router/redirect 값이 실제 page 경로와 일치
- [ ] route group이나 동적 세그먼트 규칙 반영

### State Machine

- [ ] 정의된 상태 전이가 실제 코드에서 실행됨
- [ ] 코드의 status 업데이트가 허용 전이에 포함됨
- [ ] 중간 상태에서 최종 상태로 가는 경로 누락 없음

## Timing

QA는 전체 완성 후 한 번만 하지 않는다. 모듈 완성 직후 incremental QA를 수행하면 경계면 결함이 후속 모듈로 전파되는 것을 막을 수 있다.

## Report Format

```markdown
## QA Report

### Verdict: PASS | FIX | REJECT

| Check | Status | Evidence |
|---|---|---|
| API shape | FIX | route returns object, hook expects array |

### Fix Requests
1. {file/section}: {specific fix}

### Unverified
- {scope not checked}
```
