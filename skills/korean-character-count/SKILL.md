---
name: "korean-character-count"
description: "자기소개서, 지원서, 서술형 폼처럼 글자 수 제한이 있는 한국어 텍스트를 눈대중 없이 코드로 정확히 셀 때 사용. 공백 포함/제외 글자 수, 줄 수, UTF-8 바이트, NEIS식 바이트를 함께 계산한다. 출처: NomaDamas/k-skill korean-character-count (MIT)를 Aside REPL용으로 변환."
---

# 한국어 글자 수 세기

글자 수는 절대 추정하지 않는다. 항상 REPL에서 아래 코드를 실행해 센 값만 보고한다.

```js
function countKo(text) {
  const seg = new Intl.Segmenter('ko', { granularity: 'grapheme' });
  const g = [...seg.segment(text)].map(s => s.segment);
  const isNl = s => /^(\r\n|\n|\r|\u2028|\u2029)$/.test(s);
  const isWs = s => /^\s+$/u.test(s);
  const newlines = g.filter(isNl).length;
  const neisBytes = g.reduce((n, s) => n + (isNl(s) ? 2 : /[\uAC00-\uD7A3\u3131-\u318E]/.test(s) ? 3 : /^[\x00-\x7F]$/.test(s) ? 1 : Buffer.byteLength(s, 'utf8')), 0);
  return {
    withSpaces: g.length,                       // 공백·줄바꿈 포함 (대부분 채용 사이트 기준)
    withoutSpaces: g.filter(s => !isWs(s)).length, // 공백·줄바꿈 제외
    withSpacesNoNewline: g.filter(s => !isNl(s)).length, // 줄바꿈만 제외
    lines: text.length === 0 ? 0 : newlines + 1,
    utf8Bytes: Buffer.byteLength(text, 'utf8'),
    neisBytes                                    // 한글 3B, ASCII 1B, 줄바꿈 2B
  };
}
console.log(countKo(TEXT));
```

## 규칙

- 입력을 trim하거나 정규화하지 않는다. 사이트에 붙여 넣을 그 문자열 그대로 센다.
- 결과에는 어떤 기준(공백 포함/제외, 줄바꿈 포함 여부)으로 셌는지 같이 적는다.
- 기본 보고 형식: "공백 포함 N자 / 공백 제외 M자 (제한 L자)". 제한을 넘으면 몇 자 초과인지 적는다.
- 채용 사이트마다 기준이 다를 수 있다. 사이트 입력창의 글자 수 표시가 있으면 그 값을 최종 기준으로 삼고, 코드 값과 다르면 차이를 알려준다.
- 문체 수정(korean-humanizer 등) 뒤에는 반드시 다시 센다.
