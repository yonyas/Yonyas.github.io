---
title: '[CSS] flex grow, shrink, basis에 대해'
date: 2021-07-28
category: 'CSS'
draft: false
---

반응형 웹 만들 때 flex 속성들이 헷갈리는 부분이 있어서 정리해본다.

```html
<div class="parent">
  <div class="child">Child</div>
  <div class="child">Child</div>
  <div class="child">Child</div>
</div>

.parent { display: flex; }
```

위 코드는 자식태그들에 자동적으로 아래 속성들을 넣는다.

```css
/* 자동 속성들 */
.child {
  flex: 0 1 auto;
}

/* 아래와 같다. */
/* max min idealSize 로 외우면 편하다. */
.child {
  flex-grow: 0; /* 컨텐츠의 크기만큼 차지해라 */
  flex-shrink: 1; /* 항상 같은 크기를 차지해라 */
  flex-basis: auto; /* 컨텐츠 크기만큼 차지해라 */
}
```

### flex-grow

`flex-grow: 0;` : 기본

`flex-grow: 1;` : 부모의 크기를 다 차지해라. 기본이 row니까 width를 다 차지한다고 생각.

```css
/* 다른 아이템의 3배만큼의 크기를 갖는다. */
.child-three {
  flex-grow: 3;
}
```

그런데  
두번째 child의 컨텐츠 크기가 엄청 크다면?  
grow를 무시하고 두번째 child가 제일 커진다.

즉 컨텐츠 크기가 중요하다.

### flex-shrink

`flex-shrink: 1;` : 기본. 줄어들어라. 다른 아이템들과 같게.

`flex-shrink: 0;` : 절대로 줄어들지 마라!

### flex-basis

`flex-basis: auto;` 기본. 이상적 사이즈는 컨텐츠의 사이즈이다.

## parent의 크기 전체를 차지하고자 한다면?

`width: 100%;` or `flex-basis: 100%;` or `flex-grow: 1;`  
서로가 영향을 미치기 때문에 단축어로 한번에 사용해주는 것이 한번에 볼 수 있어 좋다.  
참고로 width보다 flex-basis 우선순위가 높다.

## 코드로 생각해보기

1. 작은 화면에서는 1000px이 되지 않는다.

```css
.child-three {
  flex: 0 1 1000px;
}
```

basis say : 1000px 너비를 잡도록 노력을 해줘라. 안되면 비율이라도 많이 해줘라.  
shrink say : 줄어들 때 다른애들과 같아지도록 줄어들어라.

2. 1000px에 고정.

```css
.child-three {
  flex: 0 0 1000px;
}
```

shrink say : 절대로 줄지 마라! ==> 1000px로 고정된다. 부모를 넘든말든.

3. wrap을 한다면 어떨까?

```css
.parent {
  display: flex;
  flex-wrap: wrap;
}

.child-three {
  flex: 0 0 1000px;
}
```

두줄이 되면서 셋째자식이 밑줄로 간다.  
flex는 원래 아이템들을 한줄에 맞추도록 노력하지만 wrap을 하면 같은 공간에 맞지 않으면 한 줄을 더 생성한다.

## 출처

https://css-tricks.com/understanding-flex-grow-flex-shrink-and-flex-basis/
