---
title: '[Book] 이펙티브 타입스크립트 : 동적 데이터에 인덱스 시그니처 사용하기'
date: 2022-04-29
category: 'TypeScript'
draft: false
---

> 이펙티브 타입스크립트 아이템 15 부분을 읽으면서 정리한 내용입니다.

## 인덱스 시그니처란?

객체 타입인데 키의 이름을 마땅히 모를 때 유용한 방법.  
예시를 보자.

```ts
// [property: string] : string 이 부분이 인덱스 시그니처이다.
type Rocket = { [property: string]: string };

const rocket: Rocket = {
  name: 'Falcon 9',
  variant: 'v1.0',
  thrust: '4,940 kN',
};
```

#### 특징 3가지

- 위에 `property`라는 키의 이름은 위치만 표시하는 용도이다. 원하는 대로 네이밍하면 된다.
- 키의 타입 : string | number | symbol 의 조합. 보통은 string을 쓴다.
- 값의 타입 : 어떤 것이든 가능하다.

#### 인덱스 시그니처의 단점 4가지

- 키의 이름이 랜덤이기 때문에 실제 객체의 키의 이름과 달라도 잡을 방법이 없다. variant를 varriant 라고 써도 유효하다.
- `type Rocket = { [property: string] : {} }` 로 변경해도 문제가 없다. number 로 바꾸면 에러가 뜬다.
- 위 예시에서 `thrust` 만 number 타입을 가지고 싶을 때 구현할 방법이 없다.
- IDE 자동완성기능이 동작되지 않는다. rocket 하고 점 찍으면 name, variant, thrust 가 뜨지 않는다.

#### 결론

- 인덱스 시그니처는 부정확하므로 특정한 경우에만 사용하자.
- 특정한 경우란? 동적 데이터를 표현할 때. 키의 이름을 모를 때.

### 특정한 경우를 살펴보자

```ts
function parseCSV(input: string): { [columnName: string]: string }[] {
  const lines = input.split('\n');
  const [header, ...rows] = lines;
  return rows.map(rowStr => {
    const row: { [columnName: string]: string } = {};
    rowStr.split(',').forEach((cell, i) => {
      row[header[i]] = cell;
    });
    return row;
  });
}
```

- 우선 위 코드를 파악해보자

  - input으로 ‘가 (엔터) 나 (엔터) 다 (엔터) 라 (엔터)’ 를 넣어보면 다음과 같은 배열이 리턴된다.
  - ````ts
        [{"가": "나"}, {"가": "다"}, {"가": "라"}]
        ```
    **만약 열 이름을 알고 있는 특정한 상황이라면 단언문 as를 사용해준다.**
    ````

```ts
interface ProductRow {
  productId: string;
  name: string;
  price: string;
}

declare let csvData: string;
const products = (parseCSV(csvData) as unknown) as ProductRow[]; // as unknown !!
```

as unknown 이 없으면 알아서 사용하라고 에러가 떠주기 때문에 외울 필요는 없다.

`[columnName: string]: string` 과 `ProductRow[]`  두 형식이 충분히 겹치지 않는다고 보는 것이기 때문에 as가 필요하다.

**키의 이름이 제한되었다면 인덱스 시그니처를 쓰지 말자.**

아래와 같이 키의 이름이 a, b, c, d 중 하나일때는 이렇게 쓰는게 좋다.

```ts
interface Row2 {
  a: number;
  b?: number;
  c?: number;
  d?: number;
}
```

**키의 이름이 제한되었지만 너무 많을 경우는 두가지 대안이 있다.**

1. Record 를 사용하자

```ts
type Vec3D = Record<'x' | 'y' | 'z', number>;
// Type Vec3D = {
//  x: number;
//  y: number;
//  z: number;
// }
```

2. 매핑된 타입을 사용하자.

```ts
type Vec3D = { [k in 'x' | 'y' | 'z']: number };
// 위의 record 예제와 같다.

type ABC = { [k in 'a' | 'b' | 'c']: k extends 'b' ? string : number };
// Type ABC = {
//  a: number;
//  b: string;
//  c: number;
// }
```

위에 ABC 타입을 보면 매핑된 타입을 사용하면 키마다 별도의 타입을 사용하는 것도 가능해진다 !
