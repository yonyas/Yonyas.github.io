---
title: 'React에서 useRef 다루기'
date: '2024-06-22'
category: 'React'
draft: false
---

React에서 useRef를 사용하는 방법, 그 특징, 그리고 React에서 useRef의 동작 원리에 대해 알아보겠습니다.

## 기본 사용법

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0); // useRef 훅으로 ref를 만든다. 인자로는 initialValue가 들어간다.

  function handleClick() {
    ref.current = ref.current + 1; // ref.current로 해당 값을 사용한다.
    alert('You clicked ' + ref.current + ' times!');
  }

  return <button onClick={handleClick}>Click me!</button>;
}
```

## 특징

- Ref는 React가 추적하지 않는 비밀 주머니와 같다.
- React의 state처럼 ref 값도 리렌더 시 유지된다. 하지만 state 변경은 component를 리렌더하는 반면 ref 변경은 그렇지 않다.

## State와의 차이

- state는 value를 return하는 반면, ref는 `{ current: value }` 로 리턴한다.
- state는 변경할 경우 리렌더되는 반면, ref는 그렇지 않다.
- state는 “Immutable”하게 다뤄야 하지만, ref는 current의 value를 바로 수정하는 것이 가능하다.
- state는 렌더링 중 언제라도 값을 사용해도 되는데, ref는 사용할 때 주의가 필요하다. 왜냐하면 ref 값은 렌더링과 싱크되어 변하지 않기 때문에 헷갈릴 수 있다.

## 언제 사용할까

- setTimeout, setInterval의 ID값을 저장할 때
- DOM을 조작할 때
- JSX 계산이 필요하지 않은 객체를 저장할 때

## SetInterval에 Ref를 사용한 예

- useRef로 intervalRef를 만들어준다.
- handleStart 함수가 실행되면 setInterval의 ID를 intervalRef에 저장한다.
- handleStop 함수가 실행되면 clearInterval에 intervalRef의 값을 넣어줘서 interval 실행을 멈춘다.

```js
import { useState, useRef } from 'react';

export default function Stopwatch() {
  const [startTime, setStartTime] = useState(null);
  const [now, setNow] = useState(null);
  const intervalRef = useRef(null);

  function handleStart() {
    setStartTime(Date.now());
    setNow(Date.now());

    clearInterval(intervalRef.current);
    intervalRef.current = setInterval(() => {
      setNow(Date.now());
    }, 10);
  }

  function handleStop() {
    clearInterval(intervalRef.current);
  }

  let secondsPassed = 0;
  if (startTime != null && now != null) {
    secondsPassed = (now - startTime) / 1000;
  }

  return (
    <>
      <h1>Time passed: {secondsPassed.toFixed(3)}</h1>
      <button onClick={handleStart}>Start</button>
      <button onClick={handleStop}>Stop</button>
    </>
  );
}
```

## React 내부에서는 useRef가 어떻게 동작할까?

- 마치 setter가 사용되지 않는 useState와 같다.

```js
// Inside of React
function useRef(initialValue) {
  const [ref, unused] = useState({ current: initialValue });
  return ref;
}
```

## DOM에 사용하기

다음은 Button을 클릭했을 때 Text input에 focus하는 코드이다.

- useRef 훅으로 inputRef를 만든다.
- `<input ref={inputRef}>` 로 넣어준다. 이렇게 하면 “리액트야~ \<input\>의 DOM node를 inputRef에 넣어줘"라고 요청하는 것이다.

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>Focus the input</button>
    </>
  );
}
```

## Ref callback 사용하기

아래와 같이 list에서 표출하는 item의 개수만큼 ref를 만들고 싶을 때가 있다.

```js
<ul>
  {items.map(item => {
    // Doesn't work!
    const ref = useRef(null);
    return <li ref={ref} />;
  })}
</ul>
```

이럴 때 ref callback을 사용한다.

사용법은 아래와 같다.

- ref attribute에 function을 넣어준다.
- 해당 function은 인자로 node를 받는다.
- 이 코드에서는 map을 만들어서 key에는 list item의 name을 줬고 value에는 node를 주었다.
- `else {}`  이 부분은 메모리 누수를 막기 위해 컴포넌트가 unmount될 시 동작한다.

```js
<ul>
  {catList.map(cat => (
    <li
      key={cat}
      ref={node => {
        const map = getMap();
        if (node) {
          map.set(cat, node);
        } else {
          map.delete(cat);
        }
      }}
    >
      <img src={cat} />
    </li>
  ))}
</ul>
```

<br />
다음은 위 코드를 사용한 풀 예제이다.

- Cat Button을 누를 때마다 해당 cat이 보이는 DOM으로 스크롤을 이동시킨다.

```js
import { useRef, useState } from 'react';

export default function CatFriends() {
  const itemsRef = useRef(null);
  const [catList, setCatList] = useState(setupCatList);

  function scrollToCat(cat) {
    const map = getMap();
    const node = map.get(cat);
    node.scrollIntoView({
      behavior: 'smooth',
      block: 'nearest',
      inline: 'center',
    });
  }

  function getMap() {
    if (!itemsRef.current) {
      // Initialize the Map on first usage.
      itemsRef.current = new Map();
    }
    return itemsRef.current;
  }

  return (
    <>
      <nav>
        <button onClick={() => scrollToCat(catList[0])}>Tom</button>
        <button onClick={() => scrollToCat(catList[5])}>Maru</button>
        <button onClick={() => scrollToCat(catList[9])}>Jellylorum</button>
      </nav>
      <div>
        <ul>
          {catList.map(cat => (
            <li
              key={cat}
              ref={node => {
                const map = getMap();
                if (node) {
                  map.set(cat, node);
                } else {
                  map.delete(cat);
                }
              }}
            >
              <img src={cat} />
            </li>
          ))}
        </ul>
      </div>
    </>
  );
}

function setupCatList() {
  const catList = [];
  for (let i = 0; i < 10; i++) {
    catList.push('https://loremflickr.com/320/240/cat?lock=' + i);
  }

  return catList;
}
```

## Component에 ref 사용하기

```js
const MyInput = forwardRef((props, ref) => {
  return <input {...props} ref={ref} />;
});
```

## useImperatvieHandle의 필요성과 사용 예

위 예시에서 MyInput은 DOM 입력 요소를 노출한다. 이렇게 하면 부모 컴포넌트가 이 요소에 focus()를 호출할 수 있다. 하지만 이렇게 하면 부모 컴포넌트가 다른 작업(예: CSS 스타일 변경)을 할 수도 있다. 드문 경우지만 노출되는 기능을 제한하고 싶을 수도 있다. 이 경우 useImperativeHandle을 사용한다.

```js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef((props, ref) => {
  const realInputRef = useRef(null);
  useImperativeHandle(ref, () => ({
    // Only expose focus and nothing else
    focus() {
      realInputRef.current.focus();
    },
  }));
  return <input {...props} ref={realInputRef} />;
});

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <MyInput ref={inputRef} />
      <button onClick={handleClick}>Focus the input</button>
    </>
  );
}
```

## React는 언제 ref를 첨부할까

React에서는 모든 업데이트는 Render, Commit 두 단계로 나눌 수 있다.
Render 시에는 DOM이 아직 만들어지기 전이고, 그러므로 ref.current가 null이다.
Commit 할 때 ref.current를 첨부한다.

## 출처

- [https://react.dev/learn/referencing-values-with-refs](https://react.dev/learn/referencing-values-with-refs)

- [https://react.dev/learn/manipulating-the-dom-with-refs](https://react.dev/learn/manipulating-the-dom-with-refs)
