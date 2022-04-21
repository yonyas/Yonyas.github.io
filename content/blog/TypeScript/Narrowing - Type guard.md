---
title: '[TypeScript] Narrowing - Type guard'
date: 2022-04-17
category: 'TypeScript'
draft: false
---

> 공식문서 읽다가 타입스크립트를 사용하면서 잘 안쓰던 것들, 주의할 점들 위주로 적어봅니다.

<br />

Narrowing이란 ?  
함수에서 인자가 string, number를 받을 때, 조건문으로 코드 윗부분에서 `typeof something === number` 으로 만들어주면 아래 코드는 그 외의 타입인 `string`  으로 인식된다.

예시를 보자.

```typescript
function padLeft(padding: number | string, input: string) {
  return ' '.repeat(padding) + input; // ❌ padding type이 string일 경우 에러가 난다.
}
```

조건문으로 type 이 number일 경우를 걸어준다.

```typescript
function padLeft(padding: number | string, input: string) {
  if (typeof padding === 'number') {
    return ' '.repeat(padding) + input; // ✅
  }
  return padding + input;
}
```

🚩 주의할 점

- `typeof null`  은 `object`  이다 !!
- object 타입가드에는 `string[]` 과 `null` 이 걸린다.

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === 'object') {
    for (const s of strs) {
      console.log(s); // typeof s === string[] or null
    }
  } else if (typeof strs === 'string') {
    console.log(strs); // typeof s === string
  } else {
    // do nothing
  }
}
```

## Truthiness narrowing

JavaScript에서 if 문의 조건 타입이 항상 `boolean`  일 필요는 없다. 이를 이용한 narrowing 이 Truthiness narrowing 이다.

```ts
function getUsersOnlineMessage(numUsersOnline: number) {
  if (numUsersOnline) {
    return `${numUsersOnline}명의 유저가 접속해 있습니다.`;
  }
  return '아무도 없네요 :('; // number가 0 일 경우와 그 외 falsy
}
```

JavaScript는 if 문의 조건문을 어떻게 boolean으로 바꿀까?

falsy한 값을 보자

- `0`
- `NaN`
- `""` (the empty string)
- `0n` (the `bigint` version of zero)
- `null`
- `undefined`

🚩 주의할 점

조건문에 string 타입을 넣으면 빈 스트링 “ “ 은 타입가드에 걸리지 않는다.

마찬가지로 number 타입을 넣으면 0은 타입가드에 걸리지 않는다.

null 이 아닌 경우를 타입가드 하겠다고 사용하다가 실수하는 경우가 종종 생긴다.

```ts
if (someStringOrNumber) { ... } 
else { ... } // "", 0
```

아까 printAll 코드를 Truthiness narrowing을 이용해서 변경해보자.

```ts
function printAll(strs: string | string[] | null) {
  if (strs && typeof strs === 'object') {
    // strs && 을 추가했다.
    for (const s of strs) {
      console.log(s); // string[]
    }
  } else if (typeof strs === 'string') {
    console.log(strs); // string
  }
}
```

아래와 같이 코드를 짜면 어떤 문제가 발생할까?!!

```ts
function printAll(strs: string | string[] | null) {
  // !!!!!!!!!!!!!!!!
  //  이렇게 하지 마세요!
  // !!!!!!!!!!!!!!!!
  if (strs) {
    if (typeof strs === 'object') {
      for (const s of strs) {
        console.log(s);
      }
    } else if (typeof strs === 'string') {
      console.log(strs);
    }
  }
}
```

힌트 : 조건문에서 잡지 못하는 타입이 생긴다. 어떤 경우를 잡지 못할까 ?!

<br />
<br />
<br />
답 ) “” 빈 스트링을 잡지 못한다. 
<br />
<br />

## `in`  operator narrowing

`Object.keys(fish).includes(..)` 하던 시절 안녕

object에 해당 property가 있는지 없는지 확인하기 위해 사용하는 narrowing !

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
function move(animal: Fish | Bird) {
  if ('swim' in animal) {
    return animal.swim();
  }
  return animal.fly();
}
```

## type predicates 사용하기

일명 `user-defined type garud`

준비물 : 리턴타입이 `type predicate`  인 함수

- 아래 함수에서  `pet is Fish`  가 type predicate 임.
- 형태는 보다시피 `파라미터이름 is Type`  임.

```ts
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

```ts
let pet = getSmallPet(); //수영을 할 수도 있고 하늘을 날 수도 있는 애완동물이 있다.?!
if (isFish(pet)) {
  pet.swim(); // 수영만 해요
} else {
  pet.fly(); // 날아다니기만 해요
}
```

## Discriminated unions

이 방법은 “공통된 프로퍼티를 넣어줘서 구분하기”다.

아래와 같은 interface가 있다.

kind가 `circle` 이면 radius를 가지고

kind가 `square`  면 sideLength  를 가진다.

```ts
interface Shape {
  kind: 'circle' | 'square';
  radius?: number;
  sideLength?: number;
}
```

이렇게 짜면 어떤 문제가 있을까?!

```ts
function getArea(shape: Shape) {
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius ** 2; // ❌error: shape이 undefined일 수도 있음!
  }
}
```

자 이제 ! 를 사용할 차례인가? 왜냐하면 확실히 undefined가 아님을 내가 알고 있기 때문이다.

```ts
function getArea(shape: Shape) {
  if (shape.kind === 'circle') {
    return Math.PI * shape.radius! ** 2; // 느낌표를 추가했다.
  }
}
```

하지만 찜찜하다.

처음으로 돌아가서 수정하자.

```ts
interface Circle {
  kind: 'circle';
  radius: number;
}
interface Square {
  kind: 'square';
  sideLength: number;
}
type Shape = Circle | Square;
```

이제 다시 활용해보자.

```ts
function getArea(shape: Shape) {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;
    case 'square':
      return shape.sideLength ** 2;
  }
}
```

깔끔해졌다.

## 출처

[TypeScript 공식문서](https://www.typescriptlang.org/docs/handbook/2/narrowing.html)
