---
title: 'React router에 대해'
date: 2021-07-21
category: 'React'
draft: false
---

## Route props

### history

예를 들어 nav에 apple을 누른다. 버튼에 아래와 같이 함수를 넣어준다. 카테고리페이지로 가면서 apple을 파라미터로 가져간다.

`history.push('/category/apple')`

### match

대표적으로 match.params 로 path에 설정한 파라미터값(apple)을 가져온다.

```jsx
import React from 'react'
import ReactDOM from 'react-dom'
import { BrowserRouter as Router, Route } from 'react-router-dom'

function Category(props) {
  return <h1>{props.match.params.id}</h1> // history.push로 넣어줬던 apple
}

ReactDOM.render(
  <Router>
    <Route path="/category/:id" component={Category} />
  </Router>,
  node
)
```

### location

`localhost:3000/category?name=apple`

대표적으로 location.serach 로 url의 쿼리스트링을 가져온다. 쿼리스트링이란 엔드포인트 주소 이후에 ?부터 문자이다.

`?category=apple`

location.pathname으로는 현재페이지의 경로명을 가져온다.

`/category`

## Hooks

### Link

```jsx
<Link to="/category">Category</Link>
<Link to="/category?sort=name" />
```

### useHistory

navigate에 사용.

```jsx
let history = useHistory()

history.push('/category')
history.push('/category/1')
```

### useParams

현재 Route의 match.params에 접근하기 위해 사용

```jsx
function BlogPost() {
  let { id } = useParams()
  return <div>{id}</div>
}

ReactDOM.render(
  <Router>
    <Switch>
      <Route exact path="/">
        <HomePage />
      </Route>
      <Route path="/category/:id">
        <BlogPost />
      </Route>
    </Switch>
  </Router>,
  node
)
```

### useLocation

```jsx
function Pages() {
  let location = useLocation()
  React.useEffect(() => {
    console.log(location.search)
  }, [location])
}
```
