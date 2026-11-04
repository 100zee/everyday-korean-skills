---
name: "naver-blog-research"
description: "네이버 블로그 글을 검색하거나, 블로그 링크(blog.naver.com / m.blog.naver.com / naver.me)의 본문·사진 수·댓글을 읽어 요약할 때 사용. 베이킹 레시피 정리, 맛집·제품 후기, 기업·면접 후기 조사처럼 한국어 후기가 필요한 리서치에 쓴다. 원본: NomaDamas/k-skill naver-blog-research (MIT)의 방식을 Aside용으로 변환."
---

# 네이버 블로그 리서치

PC 주소(blog.naver.com)는 본문이 iframe 안에 있어 읽기 번거롭다. **항상 모바일 주소로 바꿔서 읽는다.**

## 1. 검색 (REPL fetch)

```js
const q = '휘낭시에 레시피';
const html = await fetch('https://search.naver.com/search.naver?ssc=tab.blog.all&query=' + encodeURIComponent(q)).then(r => r.text());
const posts = [...new Set([...html.matchAll(/https:\/\/blog\.naver\.com\/([A-Za-z0-9_\-]+)\/(\d{9,})/g)].map(m => m[0]))];
console.log(posts.slice(0, 10));
```

최신순이 필요하면 검색 결과 페이지를 브라우저로 열어 정렬을 바꾼다. 광고·협찬 글("소정의 원고료", "제품을 제공받아")은 표시해 두고 후기 신뢰도를 낮게 본다.

## 2. 본문 읽기

- `https://blog.naver.com/{blogId}/{logNo}` → `https://m.blog.naver.com/{blogId}/{logNo}`로 바꿔 fetch한다 (모바일 User-Agent 권장).
- 제목: `og:title` 메타. 본문: `se-main-container` 안의 텍스트(태그 제거). 사진: `data-lazy-src` 개수.
- naver.me 단축 링크나 fetch가 막히면 openTab으로 모바일 주소를 열고 `snapshot(page)`로 읽는다.
- 본문이 사진 위주(레시피 표가 이미지)면 해당 이미지를 screenshot/display로 직접 확인하고, 읽은 숫자만 쓴다.

## 3. 댓글 읽기

- 브라우저로 `https://m.blog.naver.com/CommentList.naver?blogId={blogId}&logNo={logNo}`를 연다.
- `page.evaluate`로 `.u_cbox_contents` 텍스트를 모은다 (같은 댓글이 중복으로 잡히면 중복 제거). 답글(작성자 답변)도 포함되면 구분해 둔다.
- 댓글에서 쓸 만한 것: 실제로 따라 해본 결과, 실패 원인과 작성자 답변, 재료 대체, 오븐 온도·시간 조정.

## 4. 요약 형식

- 글 제목 / 작성일 / 링크
- 핵심 내용 (레시피면 재료 g 단위 표 + 단계 요약)
- 댓글에서 건진 팁 (몇 개 중 몇 개가 같은 말을 하는지)
- 협찬 여부, 주의할 점

한 번에 수십 개 글을 긁지 않는다. 필요한 글만 읽는다. 다 쓴 탭은 닫는다.
