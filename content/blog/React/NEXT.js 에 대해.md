---
title: 'NEXT.js에 대해'
date: 2021-10-18
category: 'React'
draft: false
---

## NextJS 특징

- 직관적인 page-based 라우팅 시스템
- SSG 및 SSR 지원
- 자동 code spliting

## Route

pages 폴더 안의 구조에 따라 라우팅이 된다.

```jsx
pages - posts - first.js - index.js;
```

이 구조라면 index.js 는 localhost:3000/ 주소.

first.js 는 localhost:3000/posts/first 주소이다.

## Link

페이지끼리 이동하려면 Link 컴포넌트로 감싸고 a tag 를 안에 넣는다.

```jsx
import Link from 'next/link';

export default function FirstPost() {
  return (
    <h2>
      <Link href="/">
        <a>Back</a>
      </Link>
    </h2>
  );
}
```

이 Link 컴포넌트는 client-side-navigation 이 가능하다.
