---
title: '[CSS] float에 대해'
date: 2021-04-14
category: 'CSS'
draft: false
---

float는 원래 이미지와 글이 함께 있을 때 이미지 주변에 글을 감싸기 위해 사용했다.
![](https://images.velog.io/images/yonyas/post/99b3faaf-f821-44d9-82c1-31607839693e/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-04-13%2020-21-40.png)

그러다가 레이아웃 구성에도 사용하게 되었는데 원래 용도가 레이아웃 구성이 아닌만큼 float를 사용하기 위해서는 몇가지 추가로 알아야할 것들이 있다.

### `float: left` 사용시 부모의 height가 0이 된다

#### 해결방법 1

- 부모에 `overflow: hidden` (height가 자주 바뀔 것 같다면)
- height를 지정 (유지보수 문제)

_참고 : height는 auto가 기본값이고 자신이 가지고 있는 컨텐츠만큼의 크기가 된다.  
`overflow: hidden`은 자신의 컨텐츠가 넘는 영역은 가린다 = 자신의 컨텐츠만큼 보여준다._

#### 해결방법 2

- 부모에도 똑같이 `float: left`의 속성을 걸어준다.  
  (부모의 너비가 자식컨텐츠의 너비만큼으로 고정되기 때문에 너비를 반응형으로 만들어줄 때 문제가 된다.)

#### 해결방법 3

- after 사용
  - 빈 태그를 만들어서 `clear: both; height: 0; overflow: hidden;` 적용
    (쓸데없는 태그가 늘어나서 좋지 않다.)
  - ::after 가상요소를 만들어서 `clear: both` 사용

## 참고

1. https://developer.mozilla.org/ko/docs/Web/CSS/float
2. https://naradesign.github.io/float-clearing.html
