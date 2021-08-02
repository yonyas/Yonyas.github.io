---
title: 'sort()에 대해'
date: 2021-07-19
category: 'JavaScript'
draft: false
---

## sort() 의 특징

복사본이 만들어지는 것이 아니라 원 배열이 정렬된다.

## sort()와 sort((a,b) => a-b) 의 차이

```js
let array = [24, 5, 1]

array.sort() // [1,24,5]
array.sort((a, b) => a - b) // [1,5,24]
```

sort메소드는 원래 **문자열의 유니코드 포인트를 따라서 정렬**하기 때문에 숫자 정렬시 **숫자를 문자열로 변환하여 정렬**한다. 그래서 sort()만 하면 우리가 생각하는 식의 정렬이 되지 않는다.

## a, b 에는 무엇이 할당될까

a에는 뒤의 값, b에는 앞의 값이 할당된다.
a-b가 양수나 0이라면 그대로, a-b가 음수라면 앞뒤를 바꾸면서 정렬한다.

```js
let array = [21, 37, 45, -12]

array.sort(function(a, b) {
  console.log('a:', a, 'b:', b)
  return a - b
})

/*
    'a:' 37  'b:' 21
    'a:' 45  'b:' 37
    'a:' -12 'b:' 45
    'a:' -12 'b:' 37
    'a:' -12 'b:' 21
*/
```

## 프로그래머스 문제를 통해서 sort() 활용하기

**문제** : 가장 큰 수  
**설명** : 주어진 정수가 [6, 10, 2]라면 [6102, 6210, 1062, 1026, 2610, 2106]를 만들 수 있고, 이중 가장 큰 수는 6210. 6210을 string으로 리턴

```js
function solution(numbers) {
  let answer = numbers
    .map(v => v + '') // string으로 만들어준다.
    .sort((a, b) => b + a - (a + b)) // string - string = number
    .join('')

  return answer[0] === '0' ? '0' : answer
}
```

### 출처

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/sort
