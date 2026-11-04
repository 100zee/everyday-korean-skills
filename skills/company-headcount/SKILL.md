---
name: "company-headcount"
description: "채용 공고 회사의 직원 규모(국민연금 가입자 수), 당월 국민연금 고지금액, 최근 월 신규 취득/상실 인원을 공공데이터로 조회할 때 사용. '직원 N명 이상인지', '최근 사람이 많이 나가는지' 같은 지원 전 기업 검증용. 출처: NomaDamas/k-skill national-pension-workplace (MIT)를 Aside REPL용으로 변환."
---

# 회사 직원 규모 조회 (국민연금 가입 사업장)

국민연금공단이 공공데이터포털로 공개한 사업장 정보를 k-skill-proxy(공개 프록시, API 키 불필요) 경유로 조회한다. 개별 근로자 정보는 다루지 않는다.

## 조회 방법 (REPL)

```js
const name = '삼성전자(주)';          // 상호. '(주)' 위치까지 맞추면 후보가 1개로 좁혀지는 경우가 많다
const bNo = '';                       // 사업자번호 10자리(하이픈 허용). 알면 넣는다
const u = new URL('https://k-skill-proxy.nomadamas.org/v1/national-pension/workplace');
u.searchParams.set('name', name);
if (bNo) u.searchParams.set('b_no', bNo.replace(/\D/g, ''));
const res = await fetch(u, { headers: { Accept: 'application/json' } });
const j = await res.json();
console.log(res.status, JSON.stringify({ count: j.candidate_count, candidates: j.candidates?.map(c => [c.wkplNm, c.wkplRoadNmDtlAddr, c.bzowrRgstNo, c.dataCrtYm]), detail: j.detail, monthly: j.monthly_status }, null, 1));
```

## 결과 읽기

- `candidate_count`가 2 이상이면 `selected_candidate`는 null이다. 주소(`wkplRoadNmDtlAddr`)와 사업자번호 앞 6자리로 공고 회사와 맞는 후보를 고른 뒤, 정확한 상호나 사업자번호로 다시 조회한다. 확실하지 않으면 단정하지 말고 후보 목록을 보여준다.
- 사업자번호는 DART 기업개황(bizr_no), 회사 홈페이지 하단, 잡코리아/사람인 기업정보에서 찾을 수 있다.
- `detail[0].jnngpCnt`: 국민연금 가입자 수 (= 대략적인 직원 수). `crrmmNtcAmt`: 당월 고지금액(원). `vldtVlKrnNm`: 업종. `adptDt`: 적용일(설립 무렵).
- `monthly_status`: `nwAcqzrCnt` 신규 취득(입사), `lssJnngpCnt` 상실(퇴사) 인원.
- `dataCrtYm`이 자료 기준 월이다. 답할 때 "YYYY년 M월 기준"을 꼭 붙인다.

## 해석 원칙

- 점수나 "위험" 같은 라벨을 만들지 말고 사실만 전달한다.
- 가입자 수는 본사 사업장 기준이라 지점·공장이 별도 사업장이면 실제 전체 인원보다 적게 나올 수 있다. 여러 사업장이 보이면 함께 알려준다.
- 사용자가 정한 회사 규모 기준(예: 직원 N명 이상)과 비교할 때 이 숫자를 쓰되, 위 한계를 한 줄로 덧붙인다.
- 소규모·개인 사업장은 공개되지 않을 수 있다. 결과가 없으면 상호 표기를 바꿔 (예: '(주)' 앞/뒤, 띄어쓰기) 다시 시도한다.

## 실패 시

- 400: 상호 누락. 503/502: 프록시 쪽 API 키 문제라 잠시 후 재시도하거나, 공공데이터포털(https://www.data.go.kr/data/3046071/openapi.do) 또는 잡코리아/사람인 기업정보의 국민연금 인원 정보로 대체한다.
