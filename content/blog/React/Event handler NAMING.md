---
title: '[REACT] Event handler NAMING'
date: 2022-04-22
category: 'React'
draft: false
---

> handleClickButton 으로 할 지 handleButtonClick 으로 할 지 고민하다가 찾아보게 된 이벤트핸들러 네이밍에 관한 글입니다.

## Props

`onClick`  처럼 접두사 `on`  을 붙인다. 내장된 이벤트 핸들러 컨벤션도 이와 같은 컨벤션이다. 이렇게 컨벤션을 맞춤으로써 이 props가 내장된 이벤트 핸들러 기능을 포함하여 동작한다고 선언하는 의미도 있다.

## Function names

`handleClick`  처럼 접두사에 `handle` 을 붙인다.

```js
<MyComponent onClick={handleClick} />
```

`on` 은 실제 이벤트가 하는 기능에 쓰이고, `handle` 은 이벤트가 트리거됐을 때 어떤 것이 호출될 지 말해준다.

alert 창에서 버튼을 클릭하면 alert 창이 없어지게 되는데 이 동작을 `handleDismiss`  라고 네이밍 하고 싶을 수도 있다. 하지만 실제 행동(클릭)과 연관짓는 것이 더 좋다고 생각한다. `handleDismiss`  처럼 행동의 의미를 넣으면 다른 이벤트 핸들러와 헷갈릴 가능성이 크다.

## 더 복잡한 경우

alerts와 form이 있을 경우 이렇게 네이밍을 한다.
명사 `Alert` + 동사`Form` 를 형태이다.

```js
onAlertClick = { handleAlertClick };
onAlertHover = { handleAlertHover };
onFormSubmit = { handleFormSubmit };
```

handleClickAlert, handleHoverAlert 처럼 동사 + 명사보다 위 코드처럼 쓰는게 통일성 있어서 보기 좋다.

## 컴포넌트 쪼개기

네이밍과 약간 별개의 이야기지만 컴포넌트를 쪼개는 이야기를 해보겠다.
`formJs` 에 다음과 같은 두가지 핸들러가 있다고 하자.

- onRegisterationSubmit

- onLoginSubmit

이럴 경우 `formJs` 컴포넌트를 두개로 쪼개고 두 핸들러 모두`onSubmit` 으로 하는 것을 추천한다.

##

## 내장된 핸들러 이름을 사용할 경우 주의할 점

첫번째 아이템 Props와 연관되는 내용이다.
`onClick` 이나 `onSubmit` 같은 내장된 이벤트핸들러 이름을 사용할 경우 주의해야 한다.

MyComponent가 받는 `props` 에 `onClick` 이 포함되어 있다고 하자.

```js
function MyComponent(props) {
  return <div {...props}>onClick이 필요한 div입니다.</div>;
}
```

자식 요소에서 이벤트버블링이 발생하면 원치 않는 경우도 클릭이 되어버린다. 이런 경우가 있어서 주의하도록 하자 !

## 출처

[https://jaketrent.com/post/naming-event-handlers-react](https://jaketrent.com/post/naming-event-handlers-react)
