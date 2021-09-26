---
title: 'lazy loading을 써보자'
date: 2021-09-23
category: 'JavaScript'
draft: true
---

### code-splitting이란?

코드를 여러 번들로 분할하고 필요에 따라 또는 병렬적으로 로드하는 기술이다. 여기서 '필요에 따라 혹은 병렬적으로'가 lazy-loading의 정의이다. lazy-loading의 반댓말은 eager-loading이다.

> “technique of splitting your code into various bundles which can then be loaded on demand or in parallel”. [Source](https://webpack.js.org/guides/code-splitting/)

### code-spliting과 lazy-loading이 필요한 이유

큰 덩어리의 코드를 써야할 때가 있다. import 하거나 우리가 직접 작성한다. 이때 이 코드들은 main bundle 사이즈에 영향을 미쳐서 성능저하로 이어진다. 물론 빠른 인터넷이 있다면 문제 없지만 그렇지 않은 분들과 모바일데이터를 사용하는 분들을 고려해야 한다. 사용자가 필요하지 않은 코드를 로딩하지 않는 것이 좋다.

### 사용법

```js
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```

### 출처

https://medium.com/free-code-camp/how-to-use-react-lazy-and-suspense-for-components-lazy-loading-8d420ecac58  
https://ui-tricks.netlify.app/posts/2020-09-08-lazyload/
