# everyday-korean-skills

한국에서 취업 준비하고 생활하면서 실제로 쓰려고 만든 AI 에이전트 스킬 모음이에요.
[Aside](https://asidehq.com) 브라우저 에이전트에서 쓰려고 만들었지만, `SKILL.md` 표준 형식이라 Claude Code 같은 다른 에이전트에서도 읽을 수 있어요.

## 스킬 목록

| 스킬 | 하는 일 | 원본 |
|---|---|---|
| [korean-humanizer](skills/korean-humanizer) | AI가 쓴 티 나는 한국어(자소서 등)를 자연스럽게 고치기 | DaleSeo/korean-skills |
| [korean-grammar-checker](skills/korean-grammar-checker) | 맞춤법, 띄어쓰기, 문법 검사 | DaleSeo/korean-skills |
| [korean-style-guide](skills/korean-style-guide) | 문서 안 어조, 용어, 숫자 표기 통일 | DaleSeo/korean-skills |
| [korean-character-count](skills/korean-character-count) | 자소서 글자 수(공백 포함/제외)를 코드로 정확히 세기 | NomaDamas/k-skill |
| [company-headcount](skills/company-headcount) | 국민연금 가입자 수로 회사 직원 규모, 입퇴사 인원 확인 | NomaDamas/k-skill |
| [k-dart](skills/k-dart) | DART 전자공시로 기업개황, 재무, 직원 현황 조회 | NomaDamas/k-skill |
| [naver-blog-research](skills/naver-blog-research) | 네이버 블로그 본문과 댓글 읽어서 요약 | NomaDamas/k-skill |
| [interview-coach](skills/interview-coach) | 한국 신입 공채 면접 준비, 모의면접, 5항목 채점 | JunhaoXue/interview-mentor-skill |
| [cooking](skills/cooking) | 요리·베이킹 실패 원인 진단, 재료 대체, 온도 변환 | clawic/skills |
| [youth-policy-finder](skills/youth-policy-finder) | 경기도 청년 지원 정책 찾고 자격 판정 | 직접 작성 |
| [family-budget-summary](skills/family-budget-summary) | 가족 카드 내역으로 월 생활비 카톡 요약, 구독·포인트 정리 | 직접 작성 |

## 쓰는 법

- **Aside**: 계정의 `skills/user/<스킬이름>/SKILL.md` 위치에 폴더째 넣기
- **Claude Code**: `~/.claude/skills/<스킬이름>/`에 폴더째 넣기

일부 스킬에는 Aside REPL(브라우저 fetch, snapshot) 기준 코드가 들어 있어요. 다른 에이전트에서는 같은 동작을 그 도구에 맞게 바꿔 쓰면 돼요.

## 주의

- 조회·정리용 스킬이에요. 결제, 해지, 신청서 제출 같은 일은 항상 사용자 확인을 받도록 적혀 있어요.
- 정책, 공시, 사이트 메뉴 경로는 바뀔 수 있어요. 스킬도 매번 최신 공식 정보를 다시 확인하도록 되어 있어요.

## 라이선스

직접 작성한 부분은 MIT ([LICENSE](LICENSE)). 원본이 있는 스킬은 각 원작자의 MIT 라이선스를 따르고, 저작권 표시는 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)에 있어요.
