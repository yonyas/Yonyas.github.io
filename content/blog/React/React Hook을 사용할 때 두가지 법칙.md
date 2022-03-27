---
title: 'React Hook 사용할 때 두가지 법칙'
date: 2021-12-09
category: 'React'
draft: false
---

> Hooks은 React16.8버전에 등장했다. 훅을 사용하여 class 없이도 state와 React의 다른 기능들을 사용할 수 있다.

### 1. 훅은 Top Level에서만 호출하자

훅을 loops, conditions, nested functions 안에서 호출하면 안 된다. early return 전에 호출하자. 이렇게 하면 컴포넌트가 렌더될 때마다 매번 같은 순서로 훅이 호출되게 할 수 있다.
순서가 중요한 이유는 아래에 있다. 이 순서보존을 통해 React는 여러 useState 호출과 useEffect 호출 사이의 훅 상태를 올바르게 파악하고 보존할 수 있다.

### 2. React Functions 안에서만 훅을 호출하자

일반 JS함수 내에서 호출하면 안 된다. `React function component` or `custom Hooks` 에서 호출하자.

### ESLint Plugin

위 두가지 법칙을 강제하는 [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks) 플러그인. CRA에는 default로 탑재되어 있다.

```js
npm install eslint-plugin-react-hooks --save-dev

// Your ESLint configuration
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Checks rules of Hooks
    "react-hooks/exhaustive-deps": "warn" // Checks effect dependencies
  }
}

```

### 법칙의 이유

우리는 아래 코드처럼 하나의 컴포넌트에 여러개의 state와 Effect를 사용할 수 있다.

```js
function Form() {
  // 1. Use the name state variable
  const [name, setName] = useState('Mary');

  // 2. Use an effect for persisting the form
  useEffect(function persistForm() {
    localStorage.setItem('formData', name);
  });

  // 3. Use the surname state variable
  const [surname, setSurname] = useState('Poppins');

  // 4. Use an effect for updating the title
  useEffect(function updateTitle() {
    document.title = name + ' ' + surname;
  });

  // ...
}
```

위 예제에서 리액트는 어떻게 1번 state가 1번인지, 3번이 아닌지 알 수 있을까? **리액트는 훅이 호출되는 순서에 의존하여 state를 파악한다**. 위 예제가 잘 동작하는 이유는 매번 렌더될 때마다 훅이 같은 순서로 호출되기 때문이다.

```js
// ------------
// First render
// ------------
useState('Mary'); // 1. Initialize the name state variable with 'Mary'
useEffect(persistForm); // 2. Add an effect for persisting the form
useState('Poppins'); // 3. Initialize the surname state variable with 'Poppins'
useEffect(updateTitle); // 4. Add an effect for updating the title

// -------------
// Second render
// -------------
useState('Mary'); // 1. Read the name state variable (argument is ignored)
useEffect(persistForm); // 2. Replace the effect for persisting the form
useState('Poppins'); // 3. Read the surname state variable (argument is ignored)
useEffect(updateTitle); // 4. Replace the effect for updating the title

// ...
```

만약 훅 호출에 조건을 넣는다면?

```
// 🔴 We're breaking the first rule by using a Hook in a condition
  if (name !== '') {
    useEffect(function persistForm() {
      localStorage.setItem('formData', name);
    });
  }

```

- 조건절이 true이기 때문에 hook이 실행된다.
- 유저가 form을 지운다면 false가 되면서 이 훅을 건너띄게 되고 순서가 달라진다.

```
useState('Mary')           // 1. Read the name state variable (argument is ignored)
// useEffect(persistForm)  // 🔴 This Hook was skipped!
useState('Poppins')        // 🔴 2 (but was 3). Fail to read the surname state variable
useEffect(updateTitle)     // 🔴 3 (but was 4). Fail to replace the effect

```

조건을 넣고 싶다면 훅 안에 넣자

```
useEffect(function persistForm() {
    // 👍 We're not breaking the first rule anymore
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
```

## 출처

[Rules of Hooks – React](https://reactjs.org/docs/hooks-rules.html)
