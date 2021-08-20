---
title: 'Emotion 에 대해'
date: 2021-08-20
category: 'CSS'
draft: false
---

CSS-in-JS 로는 SCSS, Styled-Components 를 써봤다. SCSS의 네스팅기능과 Styled-Components의 props를 내려받아서 활용하는 기능이 좋았다. 그러나 Styled-Components의 태그를 대신하는 스타일명은 컴포넌트와 헷갈렸다. 그리고 컴포넌트명이 대문자로 가득찬 코드가 이뻐보이지 않았다.

emotion이 CSS-in-JS에서 1위를 달리고 있고 많은 기업에서 사용중이다. 어떤 특징이 있고 왜 좋은지 알아보고 싶어서 사용해보려고 한다.

![다운로드](https://user-images.githubusercontent.com/60434382/130193564-e80405bf-9249-4ed5-8264-31e71410c985.png)
[npmtrends.com](https://www.npmtrends.com/@emotion/core-vs-styled-components)

~~개발자들도 연말연초는 쉬는 것 같다..!~~

emotion 공식문서가 잘 나와있다. 그런데 코드보기가 영 불편하다. aside부분이 너무 커서 가로가 자꾸 잘려서 스크롤을 해야 한다. 쩝.

## React에서 emotion 사용하는 두가지 방법

- CSS prop
- styled

### CSS props

이 방법을 보고 옳다구나 싶었다. 왜냐하면 div, p 등 태그이름을 유지할 수 있기 때문이다. 하지만 결론적으로 이 방법은 props를 받아서 사용하려면 인라인으로 해야하기 때문에 쓰지 않기로 했다.

```js
/** @jsx jsx */   // <-- JSX Pragma 밑에 설명
import { css, jsx } from '@emotion/react'

const color = 'white'
render(
//방법 1.
  <div             // div 유지 가능!!!
    css={css`
      padding: 32px;
      &:hover {
        color: ${color};
      }
    `}
  >

//방법 2.
  <div
    css={{
      backgroundColor: 'hotpink',
      '&:hover': {
        color: 'lightgreen'
      }
    }}
  >

    Hover to change color.
  </div>
)
```

#### JSX Pragma

CSS props 방법을 사용하려면 `/** @jsx jsx */` 를 위에 붙여야 한다. 바벨을 직접 구성할 수 없을 때 이렇게 해주면 좋다. 예를 들면 CRA나 codesandbox를 사용할 경우이다. 위 코드는 jsx babel plugin이 React.createElement 대신 jsx을 사용할 수 있도록 설정한다. 음.

> Similar to a comment containing linter configuration, this configures the [jsx babel plugin](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx) to use the jsx function instead of React.createElement.
> CRA 4 이상이면 `/** @jsxImportSource @emotion/react */`를 사용하자.
> _-공식문서-_

**\*React.createElement 란?**
공식문서의 JSX Pragma를 이해하려면 이 부분을 이해해야 할 것 같다.
React에서 바벨적용 전 코드는 어떻게 되어있을까?
바벨은 내가 열심히 짠 리액트 코드를 아래와 같은 코드로 바꾸어준다.

```js
<body>
 <div id="root"></div>
 <script>
	class LikeButton extends React.Component {
  		constructor(props) { super(props);
  }
  render() { return React.createElement("button", null, "Like");  // 참고로 null 부분은 onClick같은 함수자리다.
 </script>
 <script>
 ReactDOM.render(React.createElement(LikeButton),  document.querySelector("#root"));
 </script>
</body>

// 위 코드결과
// <div id='root'><button>Like</button></div>
```

어떻게 바꿀까?
바벨은 jsx문법을 지원하는 것이다. jsx란 html+xml이다.
`<div>`는 소문자니까 html로 `<LikeButton>`은 대문자니까 xml로 변환한다.

이실직고 하자면 'React.createElement 대신 jsx을 사용할 수 있도록 설정한다'는 공식문서의 설명을 잘 모르겠다… 내 생각으로는

```js
 <div
    css={css`
      padding: 32px;
      &:hover {
        color: ${color};
      }
    `}
  >
```

위의 코드에서 css 프로퍼티의 값이 소문자인데 이걸 React.createElement로 받아들이면 태그 안에 태그니까 당연히 에러가 난다. 그래서 에러가 뜨지 않도록 어떻게저떻게 하는 것 같다…

### styled Components

> styled was heavily inspired by [styled-components](https://www.styled-components.com/) and [glamorous](https://glamorous.rocks/)

styled-components의 엄청난 영향을 받아서 그런지 정말 똑같다. 그런데 이렇게 막 갖다베껴도 되는건가. 뭐 저작권을 잘 지켜서 잘 하셨겠지만..

#### 1. props 사용법

```js
import styled from '@emotion/styled';

const Button = styled.button`
  color: ${props => (props.primary ? 'hotpink' : 'turquoise')};
`;

const Container = styled.div(props => ({
  display: 'flex',
  flexDirection: props.column && 'column',
}));

render(
  <Container column>
    <Button>This is a regular button.</Button>
    <Button primary>This is a primary button.</Button>
  </Container>
);
```

#### 2. 다른 emotion component 사용하기

백틱사용법과 객체사용법 두가지가 있다. 나는 백틱으로 통일해서 사용할 것!

**백틱 사용법**

```js
import styled from '@emotion/styled';

const Child = styled.div`
  color: red;
`;
const Parent = styled.div`
  ${Child} // 이렇게 !! {
    color: green;
  }
`;

render(
  <div>
    <Parent>
      <Child>Green because I am inside a Parent</Child>
    </Parent>
    <Child>Red because I am not inside a Parent</Child>
  </div>
);
```

**객체사용법**

```js
import styled from '@emotion/styled';

const Child = styled.div(
  {
    color: 'red',
  },
  props => ({ fontSize: props.size })
);

const Parent = styled.div({
  [Child]: {
    color: 'green',
  },
});

render(
  <div>
    <Parent>
      <Child>green</Child>
    </Parent>
    <Child>red</Child>
  </div>
);
```

Global styles랑 theme 지정은 styled-component랑 비슷해서 생략.

## 참고

[Emotion 공식문서](https://emotion.sh/docs/introduction)
[제로초님 유튜브 - 바벨 부분](https://www.youtube.com/watch?v=65vwVno1YxQ&list=PLcqDmjxt30RtqbStQqk-eYMK8N-1SYIFn&index=3)
