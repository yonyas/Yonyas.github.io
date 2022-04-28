---
title: '[Book] 이펙티브 타입스크립트 : 타입 연산과 제네릭 사용으로 반복 줄이기'
date: 2022-04-28
category: 'TypeScript'
draft: false
---

> 이펙티브 타입스크립트 아이템 14 부분을 읽으면서 정리한 내용입니다.

## DRY (Don’t Repeat Yourself) 

코드 칠 때만 주의하지 말고 타입에서도 이 규칙을 따르자.

```ts
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate {
  firstName: string;
  lastName: string;
  birth: Date;
}
```

만약 Person 에 middleName을 추가하면 PersonWithBirthDate 와는 다른 타입이 되어버린다. 
중복의 법칙을 어길 뿐만 아니라 관리도 안 되는 타입인 셈이다.
그러면 어떻게 타입에서 중복을 제거할까?

### 1. 타입에 이름을 붙인다.

```ts
function distance(a: { x: number; y: number }, b: { x: number; y: number }) {
  // 중복되는 부분을 이름을 붙인다.
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```

```ts
interface Point2D {
  x: number;
  y: number;
}
function distance(a: Point2D, b: Point2D) {
  /* ... */
}
```

그러나 중복을 찾는게 항상 쉽지는 않다.

문제 1) 아래 함수 두개의 공통타입을 뽑아서 적용시켜보자

```ts
interface Options {}

function get(url: string, opts: Options): Promise<Response> {
  return Promise.resolve(new Response());
}
function post(url: string, opts: Options): Promise<Response> {
  return Promise.resolve(new Response());
}
```

<br />
<br />
답 ) 함수표현식으로 바꿔야지 전체함수에 대한 타입을 지정할 수 있다.

```ts
type HTTPFunction = (url: string, options: Options) => Promise<Response>;
const get: HTTPFunction = (url, options) => {
  return Promise.resolve(new Response());
};
const post: HTTPFunction = (url, options) => {
  return Promise.resolve(new Response());
};
```

### 2. 키 매핑을 한다.

확장에는 extends와 & (인터섹션) 이 있다. 책에서는 인터섹션이 일반적이지 않은 방법이라고 한다.

아래와 같은 타입 두가지가 있다고 하자.

```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}

interface TopNavState {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
}
```

아래와 같이 extends 를 사용해서 확장하는 것보다

```ts
interface State extends TopNavState {
  pageContents: string;
}
```

이렇게 부분집합으로 정의하는게 더 나은 방법이다. (라고 하는데 왜일까? 잘 모르겠다)

```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
type TopNavState = {
  userId: State['userId'];
  pageTitle: State['pageTitle'];
  recentFiles: State['recentFiles'];
};
```

위 코드도 `State[’..’]` 라는 중복된 부분이 있다.

더 줄여보자

```ts
type TopNavState = {
  [k in 'userId' | 'pageTitle' | 'recentFiles']: State[k]; // ❗️❗️ 인덱스 시그니처 사용을 위해 interface 에서 type으로 변경되었다.
};
```

표준 라이브러리 Pick 을 사용할 수도 있다.

```ts
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```

문제 2 ) ActionType을 키매핑으로 수정해보자.

```ts
interface SaveAction {
  type: 'save';
  // ...
}
interface LoadAction {
  type: 'load';
  // ...
}
type Action = SaveAction | LoadAction;
type ActionType = 'save' | 'load'; // ❌ 중복됨!
```

답 )

```ts
type ActionType = Action['type'];
```

참고로 위에서 봤던 Pick을 사용하면 우리가 원하는 유니언이 아니라 객체로 잡아내서 틀리다.

```ts
type ActionRec = Pick<Action, 'type'>; // {type: "save" | "load"}
```

  <br />
  <br />
  <br />

문제 3 ) OptionsUpdate를 키 매핑으로 만들어보자

힌트 : keyof

```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
interface OptionsUpdate {
  width?: number;
  height?: number;
  color?: string;
  label?: string;
}
```

답 )

```ts
type OptionsUpdate = { [k in keyof Options] ?: Options[k] }\
```

이것도 역시 표준 라이브러리에 `Partial` 이라는 이름으로 포함되어 있다.

```ts
Partial<Options>
```

**표준 라이브러리를 보는 김에 다른 표준 라이브러리도 보자.**

함수의 리턴타입을 잡아주는 `ReturnType` 도 있다.

```ts
function getUserInfo(userId: string) {
  // ...
  return {
    userId,
    name,
    age,
    height,
    weight,
    favoriteColor,
  };
}
```

```ts
type UserInfo = ReturnType<typeof getUserInfo>; // {userId : string, name : string, ... }
```

위에 코드를 보면 typeof 를 써줬다는 것에 유의하자.

### 3. 제너릭 타입

제너릭 타입도 중복잡기에 좋다.
아래 코드를 보면 제너릭 타입으로 DancingDuo 타입을 만들어줬다.

```ts
interface Name {
  first: string;
  last: string;
}
type DancingDuo<T extends Name> = [T, T]; // [{first: string, last: string}, {first: string, last: string}]
// 🚩 extends 를 사용했다.

const couple1: DancingDuo<Name> = [
  { first: 'Fred', last: 'Astaire' },
  { first: 'Ginger', last: 'Rogers' },
]; // ✅

const couple2: DancingDuo<{ first: string }> = [
  // ~~~~~~~~~~~~~~~
  // Property 'last' is missing in type
  // '{ first: string; }' but required in type 'Name'
  { first: 'Sonny' }, // ❌ last 프로퍼티가 없다.
  { first: 'Cher' },
];
```

extends를 사용하면 제너릭 매개변수가 특정 타입을 `확장` 한다고 선언할 수 있다. 확장?! 그럼 couple2 에 middle 도 넣어보자

```ts
const couple2: DancingDuo<{ first: string; last: string; middle: string }> = [
  { first: 'Fred', last: 'Astaire', middle: 's' },
  { first: 'Fred', last: 'Astaire', middle: 's' },
]; // ✅
```

잘 동작한다. 다시 반복하자면 축소(first 하나만 받는 객체)는 안되고 확장(middle 추가)이 된다.
