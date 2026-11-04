---
name: "k-dart"
description: "금융감독원 DART 전자공시로 지원 회사의 기업개황(대표, 업종, 주소, 사업자번호, 상장 여부), 최근 공시, 재무제표(매출, 영업이익, 순이익), 직원 현황(직원 수, 평균 근속연수, 1인 평균 급여)을 조회할 때 사용. 상장사 여부 확인, 자소서 지원동기용 기업 분석, 회사 비교에 쓴다. 출처: NomaDamas/k-skill k-dart (MIT)를 Aside용으로 변환."
---

# DART 전자공시 조회

읽기 전용 조회 스킬이다. 투자 조언은 하지 않는다. 답변 끝에 "금감원 DART 공시 기준"을 짧게 남긴다.

## 경로 선택

1. **API 키가 있으면 OpenDART API** (`https://opendart.fss.or.kr/api/{endpoint}.json?crtfc_key=KEY&...`). 키는 사용자가 OpenDART(https://opendart.fss.or.kr) 회원가입 후 무료 발급한다. 키는 Aside Vault(password-manager 스킬)에서 읽어 REPL 변수로만 쓰고, 파일·채팅·로그에 평문으로 남기지 않는다.
2. **키가 없으면 DART 웹사이트** (https://dart.fss.or.kr)를 브라우저로 열어 회사 검색 → 기업개황 / 정기공시(사업보고서)에서 같은 항목을 읽는다. 사업보고서의 "직원 등의 현황", 요약재무정보 섹션을 본다.

## corp_code 얻기 (API)

대부분의 API는 8자리 `corp_code`가 필요하다. `corpCode.xml`(ZIP, 약 30MB)을 `/api/corpCode.xml?crtfc_key=KEY`에서 받아 tmp 폴더에 압축을 풀고 회사명으로 찾는다. 이미 받아 둔 파일이 있으면 재사용한다. `stock_code`가 비어 있지 않으면 상장사다.

## 자주 쓰는 endpoint

| 목적 | endpoint | 주요 인자 |
|---|---|---|
| 공시 검색 | `list.json` | corp_code, bgn_de, end_de(YYYYMMDD), pblntf_ty(A 정기공시), page_count. corp_code 없으면 기간 최대 3개월 |
| 기업개황 | `company.json` | corp_code. corp_cls: Y 유가(코스피), K 코스닥, N 코넥스, E 기타 |
| 재무제표 전체 | `fnlttSinglAcntAll.json` | corp_code, bsns_year, reprt_code(11011 사업보고서, 11012 반기, 11013 1분기, 11014 3분기), fs_div(CFS 연결 기본, OFS 개별) |
| 직원 현황 | `empSttus.json` | corp_code, bsns_year, reprt_code. 부문·성별 정규직/계약직 수, 평균 근속연수, 1인 평균 급여 |
| 배당 | `alotMatter.json` | corp_code, bsns_year, reprt_code |
| 감사의견 | `accnutAdtorNmNdAdtOpinion.json` | corp_code, bsns_year, reprt_code |
| 소송 | `lwstLg.json` | corp_code, bgn_de, end_de |

`status`가 "000"이 아니면 에러: 010/011 키 문제, 013 데이터 없음(연도·보고서 종류 재확인), 020 요청 한도 초과.

## 답변 형식 (짧게)

- 기업개황: 회사명 / 상장 구분 / 대표 / 업종 / 주소 / 설립일 / 결산월
- 재무: 최근 2~3개년 매출액, 영업이익, 당기순이익, 자산·부채·자본총계. 억/조 단위로 풀고 원본 수치도 유지
- 직원: 총 직원 수, 평균 근속연수, 1인 평균 급여 (사업보고서 기준 연도 명시)
- 자소서용으로 쓸 때는 공시에서 확인된 사실만 쓰고 출처 연도를 붙인다.
