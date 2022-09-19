---
title: 'JavaScript로 함수형코딩 배우기 by Anjana Vakil'
date: 2022-09-06
category: 'etc'
draft: false
---

인프런에 함수형 프로그래밍 응용편(유인동님) 강의를 듣고 있다.

이 강의만으로 함수형 프로그래밍은 무엇인가에 대한 이해는 사실 잘 되지 않아서 다른 자료를 읽어봤는데, 이 글은 그 중 설명이 좋았던 Anjana Vakil 의 강의 내용이다.

### 함수형 프로그래밍의 특징은 무엇일까

우선 명령형 프로그래밍은 레시피대로 요리하면서 변화하는 느낌이다. 처음에 이걸 하고 그 다음에 이걸 하고 ...
객체지향형 프로그래밍은 실제 사물을 본 딴 어떤 모델이 있고 그것들이 서로 상호작용할 때 유용하다.
함수형 프로그래밍은 모든 것이 함수로 이루어진다.

### 명령형과 함수형 코드를 살펴보자

#### imperative 

```
const name = 'Yonyas';
const greeting = "Hi, I'm ";

console.log(greeting + name); // "Hi, I'm Yonyas"

```

#### functional

간단한 코드지만 함수를 사용해서 재사용성이 높아졌다.

```
function greet(name) {
  return "Hi, I'm " + name;
}

greet("Yonyas");

```

### 함수에 side effect가 없어야 한다.

side effect는 다음을 의미한다.

- 함수 내에 console.log같은 state를 변경하고 return하는 것 외에 다른 output이 없다.
- 전역변수에 의존하지 않는다. 즉 전역변수를 바꾸거나 아예 사용하지 않고, input으로 들어온 값만 처리한다.

- 같은 input 에 같은 output이 나와야 한다.

### hof(higher order function) 를 `자주`  사용한다.

함수가 인자로 쓰이거나 함수의 return 값이 함수인 경우이다.

```
function makeAdjective(adjective) {
  return function (string) {
    return adjective + '' + string;
  }
}

const coolifier = makeAdjective('cool')
coolifier('conference') // cool conference

```

### Don’t iterate, Don’t mutate (for문 대신 map, reduce, filter 를 사용한다)

- map으로 새 배열을 만들어 사용한다. 원래 배열은 건들지 않는다.

- push 같이 mutable 한 메서드를 쓰지 않는다.(위와 같은 말이지만.. )

- 데이터가 변해버리면 어느 지점에서 바뀌었는지 알기 어렵고 디버깅이 힘들어진다.

### Persistent(Immutable) data structure for efficient immutability

- Don’t mutate 를 지키기 위해 매번 배열을 복사해서 사용하는 것은 굉장히 비효율적이다. 배열의 일부분이 바뀌었는데 전체를 복사해야 한다.
- 이를 해결하기 위한 좋은 라이브러리들이 많이 있다.(mori, immutable.js, ramda.js, underscore.js ...)
- 새 배열을 만드는 대신 바뀐 인덱스를 갈아끼우는 식으로 작동한다.

## 내 생각

프론트엔드에서는 처음에 말했던 객체지향의 특징(실제 사물을 본 딴 모델의 상호작용)이 적고, 데이터의 변화에 대한 추적의 용이성 때문에 객체지향형보다 함수형이 더 적합하다고 본다.

### 추천 아티클

- [introduction to functional progmramming by mary rose cook](https://codewords.recurse.com/issues/one/an-introduction-to-functional-programming)

### 출처

- [https://www.youtube.com/watch?v=e-5obm1G_FY](https://www.youtube.com/watch?v=e-5obm1G_FY)
