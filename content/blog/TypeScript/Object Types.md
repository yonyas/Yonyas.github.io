---
title: '[TypeScript] Object Types'
date: 2022-04-17
category: 'TypeScript'
draft: false
---

> 공식문서 읽다가 타입스크립트를 사용하면서 잘 안쓰던 것들, 주의할 점들 위주로 적어봅니다.

  <br />
  <br />
  <br />

## Setting defaults

```ts
interface PaintOptions {
  shape: Shape;
  xPos?: number;
  yPos?: number;
}
function paintShape({ shape, xPos = 0, yPos = 0 }: PaintOptions) {
  console.log(xPos);
  console.log(yPos);
}
```

- xPos, yPos에 값이 할당되지 않으면 0으로 초기화된다.

  <br />

아래와 같이 쓰고 싶은 욕구가 들 때가 있으나 안 된다.

- 타입지정과 초기값 할당을 한 번에 할 수 없다.
- JavaScript 는 객체프로퍼티에 다음과 같이 {property : something} 을 사용하면 "property의 변수명을 something 변수명으로 바꿔서 사용하는군요!" 라고 알아듣는다.

```ts
function draw({ shape: Shape, xPos: number = 100 }) {
  render(shape);
  render(xPos);
}
```

## `readonly`  Properties

```ts
interface Home {
  readonly resident: { name: string; age: number };
}
function visitForBirthday(home: Home) {
  home.resident.age++; // ✅ readonly 인데 가능하다 !!
}
function evict(home: Home) {
  home.resident = {
    // ❌ error: readonly 기 때문에 안 된다.
    name: 'Victor the Evictor',
    age: 42,
  };
}
```

## Index Signatures

프로퍼티명은 모르지만 미리 타입을 지정하고 싶을 경우 사용한다.

```typescript
interface StringArray {
  [index: number]: string;
}

const myArray: StringArray = { 1: 'mine' };
```

문제 ) myItem의 타입은?

```
const myItem = myArray[0];
```

답) string

🚩 주의할 점

1. Index signature 프로퍼티 타입은 무조건 string이나 number여야 한다.    
   \- 당연하게도 객체의 프로퍼티타입이 객체이거나 배열일 수 없다.

2. 모든 객체의 value type이 같아야 한다.  
   \- 아래코드의 경우 TypeScript는 “프로퍼티명이 name인 것만 string타입을 받을거야!” 라는 계산을 하지 못한다.

   ```typescript
   interface NumberDictionary {
     [index: string]: number;
     length: number; // ok
     name: string; // ❌error: Property 'name' of type 'string' is not assignable to 'string' index type 'number'
   }
   ```

## Generic Object Types

다음 예제코드를 읽어보자

```ts
type OrNull<Type> = Type | null;
type OneOrMany<Type> = Type | Type[];
type OneOrManyOrNull<Type> = OrNull<OneOrMany<Type>>;

type OneOrManyOrNull<Type> = OneOrMany<Type> | null;
type OneOrManyOrNullStrings = OneOrManyOrNull<string>;

type OneOrManyOrNullStrings = OneOrMany<string> | null;
```

## Tuple Types

함수의 파라미터가 배열일 경우에 optional type 깔끔하게 주는 법

```ts
type Either2dOr3d = [number, number, number?]; // 끝에 ? 를 붙였다
function setCoordinate(coord: Either2dOr3d) {
  const [x, y, z] = coord; // const z: number | undefined
}
```

## 출처

[TypeScript 공식문서](https://www.typescriptlang.org/docs/handbook/2/objects.html)
