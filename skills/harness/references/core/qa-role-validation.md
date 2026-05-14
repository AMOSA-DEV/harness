# QA Role Validation

이 문서는 하네스 전체 검증 체크리스트가 아니라, 하네스가 QA 역할을 만들 때 넣어야 할 검증 책임을 다룬다.

이 문서는 “모든 도메인을 포괄하는 QA 체크리스트”가 아니다. 핵심은 특정 웹 앱 항목 자체가 아니라, QA 역할이 존재 확인에 머물지 않고 생산자와 소비자 사이의 계약을 교차 검증해야 한다는 점이다.

하네스 전체 구조, 트리거, 드라이런, 세션 갱신 검증은 `core/harness-validation-checklist.md`를 따른다.

## 사용 방법

QA 역할을 만들 때는 먼저 도메인별 QA rubric을 새로 만든다.

1. 이번 하네스의 주요 산출물과 실패 비용을 확인한다.
2. 생산자와 소비자가 나뉘는 경계면을 찾는다.
3. 각 경계면에서 양쪽이 공유해야 하는 계약을 적는다.
4. 존재 확인 체크를 계약 검증 체크로 바꾼다.
5. 아래 웹 앱 사례가 도메인에 맞을 때만 가져온다.
6. 도메인에 맞지 않는 항목은 복사하지 않는다.

## Boundary Mismatch

두 컴포넌트가 각각 올바르게 보여도 연결 지점에서 계약이 어긋나는 결함이다.

웹 앱 하네스에서는 다음 경계면이 자주 문제가 된다:

| 경계면 | 불일치 예시 | 놓치는 이유 |
|---|---|---|
| API 응답 -> 프론트 훅 | API가 `{ projects: [...] }` 반환, 훅이 배열 기대 | 각각 따로 보면 정상처럼 보임 |
| API 필드명 -> 타입 정의 | API는 `thumbnailUrl`, 타입은 `thumbnail_url` | 타입 캐스팅이나 제네릭이 런타임 shape 차이를 숨김 |
| 파일 경로 -> 링크 | 페이지는 `/dashboard/create`, 링크는 `/create` | 파일 구조와 href를 교차 비교하지 않음 |
| 상태 전이 맵 -> 업데이트 코드 | 맵에는 전이 정의, 실제 업데이트 누락 | 맵 존재만 확인하고 실행 지점을 추적하지 않음 |
| API 엔드포인트 -> 프론트 훅 | API는 있으나 호출 hook이 없음 | API 목록과 소비자 목록을 1:1로 매핑하지 않음 |
| 즉시 응답 -> 비동기 결과 | 즉시 `{ status }` 반환인데 프론트가 최종 결과 필드 접근 | 동기/비동기 응답 구분 없이 타입만 확인 |

## Core Principle

QA는 한쪽만 읽지 않는다. 반드시 생산자와 소비자를 동시에 읽고 비교한다.

| 검증 대상 | 생산자 | 소비자 |
|---|---|---|
| API 응답 shape | route/controller 응답 | hook/client 타입 |
| 라우팅 | page 파일 경로 | href/router/redirect |
| 상태 전이 | 상태 맵 | 실제 status 업데이트 |
| DB -> API -> UI | DB 컬럼/모델 | API 응답/프론트 타입 |

존재 확인과 연결 검증을 구분한다.

| 약한 검증 | 강한 검증 |
|---|---|
| API 엔드포인트가 존재한다 | API 응답 shape과 호출측 타입/unwrap 로직이 일치한다 |
| 상태 전이 맵이 있다 | 모든 status 업데이트가 허용 전이에 포함되고 누락 전이가 없다 |
| 페이지 파일이 있다 | 모든 href/router/redirect가 실제 URL 구조와 일치한다 |
| 타입 체크가 통과한다 | 제네릭 캐스팅이나 any로 숨은 런타임 shape 불일치를 확인한다 |

## 도메인별 Rubric 작성

QA 역할에는 다음 형태의 도메인별 rubric을 넣는다:

```markdown
## 검증 대상 경계면

| 경계면 | 생산자 | 소비자 | 계약 | 검증 방법 |
|---|---|---|---|---|
| {boundary} | {producer artifact} | {consumer artifact} | {shared contract} | {how to compare both sides} |

## 판정
- PASS: 계약이 양쪽에서 일치하고 미검증 범위가 없음
- FIX: 일부 계약 불일치가 있고 수정 방향이 명확함
- REJECT: 기준, 입력, 범위가 맞지 않아 QA를 계속할 수 없음

## 미검증
- {검증하지 못한 경계면과 이유}
```

## 웹 앱 사례 템플릿

아래 체크리스트는 웹 앱용 예시다. 웹 앱, API, 프론트 훅, 라우팅, 상태 전이가 있는 하네스에만 적용한다.

### API and Frontend

- [ ] API 응답 shape과 대응 훅/클라이언트 타입이 일치
- [ ] 래핑된 응답은 소비자에서 unwrap
- [ ] snake_case/camelCase 변환 일관
- [ ] 즉시 응답과 최종 비동기 결과 구분
- [ ] API 엔드포인트와 프론트 호출 경로 매핑

검증 절차:

1. API route/controller의 실제 응답 생성 지점을 찾는다.
2. 대응 hook/client/fetch wrapper의 기대 타입과 후처리 로직을 찾는다.
3. 배열 vs 객체 래핑, 필드명, nullable, 즉시 응답과 최종 결과를 비교한다.
4. 불일치는 양쪽 중 어느 쪽을 고쳐야 하는지 파일/섹션 단위로 적는다.

### Routing

- [ ] href/router/redirect 값이 실제 page 경로와 일치
- [ ] route group이나 동적 세그먼트 규칙 반영

검증 절차:

1. 파일 기반 라우팅 구조에서 실제 URL 패턴을 추출한다.
2. 코드의 `href`, `router.push`, `redirect` 값을 수집한다.
3. route group, 동적 세그먼트, base path를 반영해 매칭한다.

### State Machine

- [ ] 정의된 상태 전이가 실제 코드에서 실행됨
- [ ] 코드의 status 업데이트가 허용 전이에 포함됨
- [ ] 중간 상태에서 최종 상태로 가는 경로 누락 없음

검증 절차:

1. 상태 전이 맵과 허용 상태 목록을 찾는다.
2. 모든 상태 업데이트 코드를 검색한다.
3. 코드에는 있는데 맵에 없는 전이, 맵에는 있는데 실행되지 않는 전이를 나눈다.
4. 프론트의 상태 기반 분기가 실제 도달 가능한 상태를 기준으로 작성됐는지 본다.

## Timing

QA는 전체 완성 후 한 번만 하지 않는다. 생산자/소비자 경계면이 생긴 직후 incremental QA를 수행하면 결함이 후속 모듈로 전파되는 것을 막을 수 있다.

빌드 하네스에서 QA 역할을 만들 때는 읽기 전용 역할만으로 충분한지 먼저 판단한다. 스크립트 실행, 테스트 실행, 수정 제안이 필요하면 런타임에서 허용되는 일반 worker 역할로 둔다. 단, QA가 직접 수정까지 할지, 수정 요청 리포트만 낼지는 오케스트레이터에 명시한다.

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

## QA Role Template

```markdown
## 핵심 역할
스펙 준수와 도메인별 경계면 계약 정합성을 검증한다.

## 검증 우선순위
1. 생산자/소비자 계약 정합성
2. 기능 스펙 준수
3. 사용자 경험 또는 산출물 품질
4. 코드/문서/데이터 품질

## 방법
- 생산자와 소비자를 동시에 읽는다.
- 도메인별 QA rubric을 먼저 만든다.
- 경계면 이슈는 양쪽 책임 범위를 함께 표시한다.
- PASS, FIX, REJECT 중 하나로 판정한다.
- 미검증 범위는 숨기지 않고 별도 목록으로 남긴다.
```

## 웹 앱 결함 예시

| 버그 | 경계면 | 원인 |
|---|---|---|
| `projects?.filter is not a function` | API -> 훅 | API가 `{projects: []}` 반환, 훅이 배열 기대 |
| 대시보드 링크 404 | 파일 경로 -> href | `/dashboard/` 접두사 누락 |
| 테마 이미지 안 보임 | API -> 컴포넌트 | `thumbnailUrl` vs `thumbnail_url` |
| 테마 선택 저장 안 됨 | API -> 훅 | API 존재, 훅 없음 |
| 생성 페이지 대기 | 상태 전이 -> 코드 | 최종 상태 전이 코드 누락 |
| `data.failedIndices` crash | 즉시 응답 -> 프론트 | 백그라운드 결과를 즉시 응답에서 접근 |
