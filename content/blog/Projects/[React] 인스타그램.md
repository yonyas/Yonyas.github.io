---
title: '[React] 인스타그램 따라만들기'
date: 2021-05-15
category: 'Projects'
draft: false
---

> ### **목차**
>
> [1-1. 로그인 페이지 마크다운 및 로그인 유효성 검사](#인스타1-1)  
> [1-2. 로그인, 회원가입 기능](#인스타1-2)  
> [2-1. 메인페이지 컴포넌트 분리](#인스타2-1)  
> [2-2-1. 메인페이지 Nav.js](#인스타2-2-1)  
> [2-2-2. 메인페이지 Feed.js](#인스타2-2-2)  
> [2-3-1. 메인페이지 Article.js](#인스타2-3-1)

저번주에 자바스크립트로 인스타그램 로그인 페이지와 메인페이지를 구현했는데
이번주에는 리액트로 구현해보았다 !

<h2 style="color:#45963f">작업기간</h2>
2021.4.28 ~ 2021.5.6.

<h2 style="color:#45963f">기술스택</h2>

- HTML/CSS
- JavaScript(ES6+)
- React
- SASS

<h2 style="color:#45963f">주요 구현사항</h2>

- 로그인 id, password 에 특정한 기준이 맞아야 로그인버튼 활성화 기능
- 백엔드와 협업해서 회원가입, 로그인 기능 구현 및 localStorage에 토큰 넣어보기
- 추가하기 !!

<h2 style="color:#45963f">구현 화면 및 코드설명</h2>

## 1. 로그인 페이지

<h3 id="인스타1-1">1-1. 마크다운 및 로그인 유효성 검사</h3>

![](https://images.velog.io/images/yonyas/post/18f6a734-24f5-4b52-a360-8ca57d859882/instalogin.gif)

**(1) state 만들기**

- input value를 담을 id, pw 를 만들어 주었다.

```js
class Login extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      id: '',
      pw: '',
    };
  }
```

**(2) 입력한 내용 input에 반영**

- 배열 비구조화 할당을 이용해서 input의 name값을 키값으로 넣어주었다.

```js
onChangeInput = e => {
  const { name, value } = e.target
  this.setState({
    [name]: value,
  })
}
```

**(3) 유효성 검사**

- validation 변수를 만들어서 id는 '@'를 포함할 때, pw는 5자리 숫자가 넘을 때 버튼이 활성화 되도록 했다.
- render() 아래에 validation 을 넣어야 바로바로 반영이 되었다.
- 다른 방법으로는 componentDidUpdate를 사용할 수 있었다.

```js
  render() {
    const validation = this.state.id.includes('@') && this.state.pw.length > 4;
    return (
      <div className="LoginJY">
        <main className="LoginBox">
          <h1 className="title">Westagram</h1>
          <div className="login">
            <input
              className="loginInputs loginId"
              name="id"
              value={this.state.id}
              onChange={this.onChangeInput}
              type="text"
              placeholder="전화번호, 사용자 이름 또는 이메일"
            />
            <input
              className="loginInputs loginPw"
              name="pw"
              value={this.state.pw}
              onChange={this.onChangeInput}
              type="password"
              placeholder="비밀번호"
            />
          </div>
          <button
            className={`loginBtn ${validation ? 'blueBackgroundColor' : null}`}
            onClick={this.onClickLoginBtn}
          {/*------------------------------------ */}
          {/* 위 onClickLoginBtn 함수는 아래에서 설명! */}
          {/*------------------------------------ */}
          >
            로그인
          </button>
```

<h3 id="인스타1-2">1-2. 로그인, 회원가입 기능</h3>

백엔드와 처음 협업해보았다 !
현재 만든 버튼이 로그인버튼 하나이기 때문에 fetch의 API를 login 과 signup 으로 직접 바꿔가면서 실행했다.
id, pw를 입력하면 백엔드분 화면에서 바로바로 떴는데 신기했다 😃

- 백엔드에서 키값을 어떻게 썼는지 파악하는게 중요했다. password로 할 수도 있고 pw로 할 수도 있다.
- 로그인이 성공하면 main페이지로 넘어가게 withRouter를 사용했다.
- 로그인이 성공하면 localStorage에 토큰을 담아보았다. 나중에 이용자가 로그인한 상태인지 아닌지, 댓글을 달 때 이 사람의 id가 무엇인지 등 파악하는데 토큰이 쓰인다고 한다.

```js
onClickLoginBtn = () => {
  fetch('http://10.58.7.181:8000/user/login', {
    method: 'POST',
    body: JSON.stringify({
      email: this.state.id,
      password: this.state.pw,
    }),
  })
    .then(response => response.json())
    .then(result => {
      if (result.message === 'SUCCESS') {
        this.props.history.push('/mainjy')
        localStorage.setItem('access-token', result.ACCESS_TOKEN)
      }
    })
}
```

## 2. 메인 페이지

![](https://images.velog.io/images/yonyas/post/781d83c1-91e2-4950-bf62-08c112214a51/instamain.gif)

<h3 id="인스타2-1">2-1. 컴포넌트 분리</h3>

자바스크립트로 장황하게 쓰여있던 코드들을 컴포넌트로 나름 쪼개보았다 !

![](https://images.velog.io/images/yonyas/post/c9e08430-b329-4de5-bf73-5efd76faa672/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-05-14%2008-22-23.png)

- Nav에서 my Account 버튼을 누르면 아까 로그인 창으로 연동되게 했다.
- Feed.js로 왼쪽부분 큰 틀을 잡고 Article.js 로 구분해서 컴포넌트를 재사용했다.
- Aside도 재사용되는 부분이 있어서 분리해주었다.

### 2-2. 컴포넌트 별 사용한 기능 설명

#### <p id="인스타2-2-1">(1) Nav.js</p>

- withRouter 사용해서 로그인창으로 이동 구현 !
- Router 사용에는 `<Link>`컴포넌트 사용과 `withRouterHOC`의 두가지 방법이 있다.
- withRouter 는 복잡해보이지만 조건을 걸 때 용이하다. 예를 들어 로그인 성공시 로그인 화면으로 가는 것!

```js
import React, { Component } from 'react'
import { withRouter } from 'react-router-dom'
import './Nav.scss'

class Nav extends Component {
  constructor() {
    super()
    this.state = {
      navLink: [
        {
          id: 1,
          imgAlt: '추천하는 사람들',
          imgSrc:
            'https://s3.ap-northeast-2.amazonaws.com/cdn.wecode.co.kr/bearu/explore.png',
        },
        {
          id: 2,
          imgAlt: '새롭게 나를 팔로우한 사람들',
          imgSrc:
            'https://s3.ap-northeast-2.amazonaws.com/cdn.wecode.co.kr/bearu/heart.png',
        },
        {
          id: 3,
          imgAlt: '내 계정',
          imgSrc:
            'https://s3.ap-northeast-2.amazonaws.com/cdn.wecode.co.kr/bearu/profile.png',
        },
      ],
    }
  }

  goToMain = () => {
    this.props.history.push('/loginjy')
  }

  render() {
    return (
      <nav className="Nav">
        <a className="navLogo" href="main.html">
          <img alt="위스타그램 로고" src="/images/leejiyon/instagram.png" />
          <div className="navLogoTitle">Westagram</div>
        </a>
        <div className="navSearchWrap">
          <input className="navSearch" type="text" placeholder="검색" />
        </div>
        <ul className="navLink">
          {this.state.navLink.map(link => (
            <li key={link.id}>
              <img alt={link.imgAlt} src={link.imgSrc} />
            </li>
          ))}
        </ul>
      </nav>
    )
  }
}

export default withRouter(Nav)
```

#### <p id="인스타2-2-2">(2) Feed.js</p>

- 목데이터를 사용해보았다 !
- 나중에는 백에서 어떤 형식으로 줄지 잘 상의하면서 해야겠지 요 .. ?

```json
[
  {
    "header": {
      "headerId": "jiyon",
      "alt": "하오 이미지",
      "className": "profileImg",
      "src": "/images/leejiyon/hao.jpg"
    },
    "articleImg": {
      "alt": "산 정상",
      "className": "articleImg",
      "src": "images/leejiyon/nature.jpg"
    },
    "myComment": "백두산 정상"
  },
  {
    "header": {
      "headerId": "dongseok",
      "alt": "전동석 이미지",
      "className": "profileImg",
      "src": "/images/leejiyon/dongseok.jpg"
    },
    "articleImg": {
      "alt": "아이유",
      "className": "articleImg",
      "src": "images/leejiyon/iu.jpg"
    },
    "myComment": "아이유 안녕"
  }
]
```

- 아래는 목데이터 받아서 state 에 저장시키는 코드

```js
class Feeds extends Component {
  state = {
    articleList: [],
  };

  componentDidMount() {
    fetch('http://localhost:3000/data/leejiyon/articledata.json', {
      method: 'GET',
    })
      .then(res => res.json())
      .then(articleData => {
        this.setState({
          articleList: articleData,
        });
      });
  }
```

#### <p id="인스타2-3-1">(3-1) Article.js - 데이터 키값 id 를 ref로 사용</p>

- 댓글리스트 id를 state 에 담지 말고 ref 로 따로 빼서 렌더링 되지 않게 하기
- 현재 댓글데이터가 3개 있어서 현재 current를 4로 넣어주었다.

```js
  componentDidMount() {
    this.idRef.current = 4;
  }
```

- 댓글이 달리면 idRef를 ++해서 적용시켜주었다.

```js
  componentDidUpdate(prevProps, prevState) {
    if (prevState.userComments.length !== this.state.userComments.length) {
      this.idRef.current += 1;
    }
  }

  onClickPostBtn = () => {
    if (!this.state.commentInputValue.length) {
      return;
    }
    this.setState({
      userComments: [
        ...this.state.userComments,
        {
          id: this.idRef.current,
          userId: this.state.currentUser,
          comment: this.state.commentInputValue,
          showTrashBtn: true,
        },
      ],
      commentInputValue: '',
    });
    this.inputRef.current.focus();
  };
```

- hooks를 사용하면 useCallback으로 묶으면 편한 것 같은데 클래스컴포넌트로 구현할 때 ref를 이렇게 다루는 것은 복잡했다. (쉬운 방법이 있을 수 있지만 ..!)

#### <p>(3-2) Article.js - 댓글 각각 좋아요 삭제 누르기</p>

- 댓글의 좋아요나 삭제를 누르면 id값을 이용해서 상태를 변경했다.
- 수정한 코드는 map으로 돌려서 클릭한 댓글의 liked상태를 변화시키고 안클릭한 댓글은 그대로 두고 setState를 했다.

```js
// ------------ 현재 userComments의 구조 --------------
  userComments: [
      {
        id: 1,
        userId: 'iu',
        comment: '아자아자',
        showTrashBtn: false,
        liked: true,
      },
      {
        id: 2,
        userId: 'jiyon',
        comment: '공부를 하자',
        showTrashBtn: false,
        liked: false,
      },
      {
        id: 3,
        userId: 'Shaman_king',
        comment: '벨로그 쓰기',
        showTrashBtn: false,
        liked: false,
      },

// ------------ 처음 코드 -------------
  onClickCommentHeartBtn = id => {
    this.state.userComments.filter((comment, i) => {
    const userComments = [...this.state.userComments];
    if (comment.id === id) {
      comment.liked = !comment.liked;
      userComments[i] = comment;
      this.setState({
        userComments: userComments,
      });
    }
  };
```

```js
// ------------ 수정한 코드 -------------
onClickCommentHeartBtn = id => {
  this.setState({
    userComments: this.state.userComments.map(comment =>
      comment.id === id ? { ...comment, liked: !comment.liked } : comment
    ),
  })
}
```

```js
onClickDeleteBtn = clickedComment => {
  this.setState({
    userComments: this.state.userComments.filter(
      user => user.id !== clickedComment.id
    ),
  })
}
```
