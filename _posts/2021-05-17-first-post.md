---
title:  "MarkDown 사용법"
excerpt: "마크다운 문법정리"

categories:
  - Blog
tags:
  - [Blog, MarkDown]

toc: true
toc_sticky: true
 
date: 2021-05-17
last_modified_at: 2021-05-17
---

# 마크다운 문법(syntax)

## 제목(Header)

- # 제목 1 (#제목)
- ## 제목 2 (##제목)
- ### 제목 3 (###제목)
- #### 제목 4 (####제목)
- ##### 제목 5 (#####제목)
- ###### 제목 6 (######제목)
  
 <br>

  ## 강조(Emphasis)
- 이텔릭체는 *별표( * )* 혹은 _언더바( _ )_ 
- 두껍게는 **별표( ** )** 혹은 __언더바( __ )__ 
- **_이텔릭체_와 두껍게**를 같이 사용 가능
- 취소선은 ~~물결표시( ~~ )~~ 
- <u>밑줄</u>은 ( < u >< /u > )  

<br>

## 목록(List)
1. 순서가 필요한 목록
2. 순서가 필요한 목록
   - 순서가 필요하지 않은 목록1
   - 순서가 필요하지 않은 목록2

 순서가 필요하지 않은 목록에 사용 가능한 기호
-  대쉬(-), 별표(*), 더하기(+)

<br>

## 링크(Links)
[GOOGLE](https://google.com)

[NAVER](https://naver.com "링크 설명(title)을 작성하세요.")

[상대적 참조](../users/login)

[Dribbble][Dribbble link]

[GitHub][1]

문서 안에서 [참조 링크]를 그대로 사용할 수도 있습니다.

다음과 같이 문서 내 일반 URL이나 꺾쇠 괄호(`< >`, Angle Brackets)안의 URL은 자동으로 링크를 사용합니다.
구글 홈페이지: https://google.com
네이버 홈페이지: <https://naver.com>

[Dribbble link]: https://dribbble.com
[1]: https://github.com
[참조 링크]: https://naver.com "네이버로 이동합니다!"

<br>

## 표(Table)

| 값 | 의미 | 기본값 |
|---|:---:|---:|
| `static` | 유형(기준) 없음 / 배치 불가능 | `static` |
| `relative` | 요소 자신을 기준으로 배치 |  |
| `absolute` | 위치 상 부모(조상)요소를 기준으로 배치 |  |
| `fixed` | 브라우저 창을 기준으로 배치 |  |

값 | 의미 | 기본값
---|:---:|---:
`static` | 유형(기준) 없음 / 배치 불가능 | `static`
`relative` | 요소 **자신**을 기준으로 배치 |
`absolute` | 위치 상 **_부모_(조상)요소**를 기준으로 배치 |
`fixed` | **브라우저 창**을 기준으로 배치 |

<br>

## 블록(block) 코드 강조
`를 3번이상 입력하고 코드 종류 적기 ( ``` )

```html
<a href="https://www.google.co.kr/" target="_blank">GOOGLE</a>
```

```javascript
function func() {
  var a = 'AAA';
  return a;
}
```
```css
.list > li {
  position: absolute;
  top: 40px;
}
```

<br>

## 수평선(Horizontal rule)

---
(---)
***
(***)
___
(___)

<br>

## 줄바꿈(Line Breaks)
 2번의 띄어쓰기나 < br > 사용
