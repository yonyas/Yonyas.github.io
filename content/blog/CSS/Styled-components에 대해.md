---
title: 'Styled-components 에 대해'
date: 2021-07-19
category: 'CSS'
draft: false
---

### styled-components 특징

- **Automatic critical CSS** : 어떤 컴포넌트가 렌더되고 있는지 자동으로 확인해서 스타일 적용 여부를 결정한다. 이는 사용자가 적은 양의 코드를 로드할 수 있게 해서 속도에 좋다.

- **No class name bugs** : 고유의 클래스명을 자동으로 생성한다. 코드 중복을 막아준다. 하지만 이로 인해 개발자도구에서 코드를 볼 때 어떤 줄인지 알 수 없다.
  `import styled from 'styled-components/macro';`  
  뒤에 /macro를 넣어주면 스타일드컴포넌트명을 알 수 있다.

- **Easier deletion of CSS** : 컴포넌트에 스타일드컴포넌트가 붙어있는 형태기 때문에 스타일이 사용중인지 쉽게 알 수 있다. 삭제도 쉽다.
- **Simple dynamic styling** : props와 global theme을 이용해서 스타일을 쉽게 수정할 수 있다.

### 사용법

#### 1. props에 따라 스타일 변경

```jsx
const Button = styled.button`
  background: ${props => (props.primary ? 'palevioletred' : 'white')};
  color: ${props => (props.primary ? 'white' : 'palevioletred')};
  border: 1px solid ${props => props.borderColor || 'black'};
`

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
    <Button borderColor="red">Primary</Button>
  </div>
)
```

#### 2. 상속 스타일

공통클래스 적용할 때 편하다.

```jsx
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  border: 2px solid palevioletred;
`

const TomatoButton = styled(Button)`
  // 이 형태는 Button 의 스타일을 상속하면서 지정한 스타일을 override 한다.
  color: tomato;
  border-color: tomato;
`

render(
  <div>
    <Button>Normal Button</Button>
    <TomatoButton>Tomato Button</TomatoButton>
  </div>
)
```

#### 3. Link (react-router-dom)

```jsx
const Link = ({ className, children }) => (
  <a className={className}>{children}</a>
)

//Extend Link
const StyledLink = styled(Link)`
  color: palevioletred;
  font-weight: bold;
`

render(
  <div>
    <Link>Unstyled, boring Link</Link>
    <StyledLink>Styled, exciting Link</StyledLink>
  </div>
)
```

#### 4. .attrs 이용하기

```jsx
const Input = styled.input.attrs(props => ({
  // static props
  type: 'text',

  // dynamic ones
  size: props.size || '1em',
}))`
  // 동적으로 계산된 props 사용
  margin: ${props => props.size};
  padding: ${props => props.size};
`

const PasswordInput = styled(Input).attrs({
  type: 'password',
})`
  border: 1px solid red;
`

render(
  <div>
    <Input placeholder="text input" size="2em" />
    <PasswordInput placeholder="pw input" />
  </div>
)
```

#### 5. GlobalStyles 사용하기

reset도 편하게 사용할 수 있다.

```jsx
// GlobalStyles.js

import { createGlobalStyle } from 'styled-components'
import reset from 'styled-reset'

const GlobalStyles = createGlobalStyle`
    ${reset} 

    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    li {
  	  list-style: none;
  	}
`

export default GlobalStyles
```

적용하기

```jsx
// index.js

import React from 'react'
import ReactDOM from 'react-dom'
import GlobalStyles from './styles/GlobalStyles'
import Routes from './Routes'

ReactDOM.render(
  <>
    <Routes />
    <GlobalStyles /> // 이렇게 넣어주면 전체 적용된다.
  </>,
  document.getElementById('root')
)
```

#### 6. style.css 등 theme 지정하기

테마 만들기

```jsx
// theme.js

const theme = {
  mainBlue: '#00c7f5',
  backgroundGrey: '#f5f5f5',
}

export default theme
```

적용하기

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import { ThemeProvider } from 'styled-components'
import theme from './theme'
import Routes from './Routes'

ReactDOM.render(
  <>
    <ThemeProvider theme={theme}>
      <Routes />
    </ThemeProvider>
  </>,
  document.getElementById('root')
)
```

사용하기

```jsx
//사용할 아무파일.js

const ProfileName = styled.span`
  color: ${({ theme }) => theme.fontMainBlack};
`
```

#### 7. variable.css 같은 mixin 사용하기

만들기

```jsx
// variables.js
import { css } from 'styled-components'

export const flexSet = (justify = 'center', align = 'center') => css`
  display: flex;
  justify-content: ${justify};
  align-items: ${align};
`

export const mediaQuery = {
  BREAK_POINT_MOBILE: 760,
  BREAK_POINT_TABLET: 1060,
}

export const mainPadding = (desktop = '60px', tablet = '40px') => css`
  padding: 10px 60px;

  @media only screen and (max-width: ${mediaQuery.BREAK_POINT_TABLET}px) {
    padding: 10px 40px;
  }
`
```

사용하기

```jsx
//사용할.js
import { flexSet } from '../../styles/Variable'
import { mediaQuery } from '../../styles/Variable'
import { mainPadding } from '../../styles/Variable'

const BottomNavContainerInnerWrapper = styled.div`
  ${flexSet()}
  max-width: 1256px;
  height: 50px;
  margin: 0 auto;
  ${mainPadding()}
`
```

### 불편했던 점

컴포넌트와 스타일드컴포넌트가 동일하게 생겨서 헷갈린다.

### 참고

https://styled-components.com/docs

```

```
