---
title: '[Book] 이펙티브 타입스크립트 : 타입 넓히기'
date: 2022-05-03
category: 'TypeScript'
draft: false
---

> 이펙티브 타입스크립트 아이템 21 부분을 읽으면서 정리한 내용입니다.

<br />
  
제목 때문에 헷갈렸는데 읽어보니까 **타입스크립트의 관점에서 타입 넓히기**란 뜻이다. 개발자 관점에서는 **추론될 때는 타입이 넓혀져서 타입추론이 되는구나~** 라고 생각하면될 것 같다.

변수를 초기화할 때 타입이 명시되어 있지 않으면 타입스크립트의 타입체커는 타입을 명시해야 하는데 이 때 지정된 값을 가지고 할당 가능한 값들의 집합을 유추해야 한다. 이 과정을 ‘타입 넓히기’라고 한다.

#### 예제 1 

3D 벡터를 다루는 라이브러리 코드를 살펴보자

```ts
interface Vector3 {
  x: number;
  y: number;
  z: number;
}
function getComponent(vector: Vector3, axis: 'x' | 'y' | 'z') {
  return vector[axis];
}
```

위 라이브러리를 사용하는 코드이다.

```ts
let x = 'x'; // type is 'string'
let vec = { x: 10, y: 20, z: 30 };
getComponent(vec, x); //❌ error : 'string'형식의 인수는 'x'|'y'|'z' 형식의 매개변수에 할당할 수 없습니다.
```

`let x` 임에 유의하자. const 일 경우는 ‘x’ 로 추론된다.

string타입은  `'x'|'y'|'z'` 타입에 할당이 불가능하므로 오류가 발생했다.

#### 예제 2

```ts
const mixed = ['x', 1];
```

mixed는 어떤 타입으로 추론될까? 추론될 수 있는 타입 후보는 아래와 같이 굉장히 많다.

```ts
('x'|1)[]
['x', 1]
[string, number]
readonly [string, number]
(string|number)[]
readonly (string|number)[]
[any, any]
any[]

```

타입스크립트는 `(string|number)\[\]`로 추측한다.

## 넓히기 제어

넓히기 과정을 제어할 수 있는 방법이 있다.

#### 1. const 사용하기

```ts
const x = 'x'; // type 'x'
```

x가 재할당될 가능성이 없으므로 좁은 타입 “x”로 추론할 수 있는 것이다.

그러나 이 방법도 단점은 있다. 바로 객체와 배열의 경우이다.

```ts
const v = { x: 1 };
let v2 = { x: 2 };
```

위 둘 타입은 {x: number}로 같다. const 지만 `{x: 1}` 로 추론되지 않는다.

#### 2. 명시적으로 타입 지정하기

```ts
const v: { x: 1 | 3 | 5 } = {
  x: 1,
}; // Type is { x: 1 | 3 | 5; }
```

#### 3. const 단언문 사용하기

as const를 사용하면 타입스크립트는 최대한 좁은 타입으로 추론한다.

```ts
const v1 = {
  x: 1,
  y: 2,
}; // Type is { x: number; y: number; }

const v2 = {
  x: 1 as const,
  y: 2,
}; // Type is { x: 1; y: number; }

const v3 = {
  x: 1,
  y: 2,
} as const; // Type is { readonly x: 1; readonly y: 2; }

const a1 = [1, 2, 3]; // Type is number[]

const a2 = [1, 2, 3] as const; // Type is readonly [1,2,3]
```
