---
title: 'HashRouter vs BrowserRouter'
date: 2022-09-19
category: 'React'
draft: false
---

React Router 라이브러리를 사용할 때 HashRouter나 BrowserRouter를 사용한다.

이 둘의 차이점은 뭘까?

### 배경지식

페이지 링크를 누르거나 새로고침 할 경우 브라우저는 현재 route로 서버에 데이터를 요청한다.

## HashRouter

URL에 `#` symbol을 사용한다. 그러면 # 뒤의 주소는 서버에 요청할 때 무시된다. 예를 들어 `www.website.com/#/user/john`  이라면 서버는 `www.website.com`  만 받게 된다. # 뒤의 주소는 클라이언트에서 처리하면 된다.

## BrowserRouter

UI와 현재 URL이 일치한다.
만약 client에 route는 있는데 서버에 없는 경우 404 에러를 받게 된다.

### 출처

- [react router 공식문서](<[https://reactrouter.com/en/main/router-components/browser-router](https://reactrouter.com/en/main/router-components/browser-router)>)
- [https://stackoverflow.com/questions/51974369/what-is-the-difference-between-hashrouter-and-browserrouter-in-react](https://stackoverflow.com/questions/51974369/what-is-the-difference-between-hashrouter-and-browserrouter-in-react)
