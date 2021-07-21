---
title: '코드스피츠 Programming101 - 1강'
date: 2021-07-15 21:49:00
category: 'Lecture'
draft: false
---

사람의 머리는 똑똑하지 않다. 기억할 수 있는 저장수의 길이는 최대 7줄.  
그래서 스코프는 좁게 변수 라이프사이클은 짧게 유지해야 한다.

또한 바로 어려운 for문을 짤 수 없다.  
**숟가락 얹기 재귀 -> 꼬리물기 최적화 재귀 -> for문으로 기게적 번역** 단계를 거쳐서 어려운 for문도 짤 수 있게 될 것이다.

아래는 1~10까지의 합을 구하는 코드이다.

```jsx
//숟가락 얹기
1. const sum = v => v > 1 ? v + sum(v-1) : 1;

//꼬리물기 최적화
2. const _sum = (v, acc = 0) ⇒ v > 1 ? _sum(v-1, acc+v) : acc+1;
2-1. const sum = v => _sum(v, 0)  // 사용자는 v값만 넣으면 된다.

//3. for문 기계적 번역
const v = 10; // 상수
let acc = 0; // 저장소
for (let i = v; i > 1; i--) acc += i; // i 는 제어변수 중 **카운터**
acc += 1;

//4. 결과적으로 짜야하는 함수
const sum = v => {
	let acc = 0;
	for (let i = v; i > 1; i--) acc += i;
	acc += 1;
	return acc
}
```

## 변수의 종류 설명

**상수** : 특정 컨텍스트(해결하려는 문제)하에서 미리 주어진 값, 함수 입장에서 인자는 상수  
**저장소** : 스토리지(알고리즘이 진행되면서 결과값을 누적할(저장할)필요가 있는 변수  
**카운터** : 몇 번 도는지 갯수 세기

## 스택오버플로어에 걸리지 않게 스택을 쌓지 않는 방법.

스택오버플로어의 기준은 브라우저 엔진마다 컴퓨터마다 등등 너무 다양하다.
문제는 위의 제 1 코드에서 sum(1000000) 정도 돌리면 스택오버플로어에 걸린다.  
틀린 코드가 아님에도 엔진이 돌다가 '어? 왜이렇게 많이 돌아? 이거이거 에러같은데 그만 돌아야겠네~'하고 스택오버플로어를 내뿜는다.  
해결은? **for문으로 짜야 한다.**

### 해보자

함수의 반환값의 복귀점을 지정해주면 되는데 js 는 안된다. 함수가 끝나면 시작한 그 지점으로 복귀한다. 복귀점 지정이 따로 안된다.

위의 1코드는 sum(v-1)을 돌고 `v +` 을 해줘야 하기 때문에 즉 할일이 남아있기 때문에 스택을 제거할 수 없다.  
2코드는 그래서 인자로 넣어줬다. 돌아와서 할일이 없게끔.

**4코드가 궁극적으로 짜야 하는 코드.** 이렇게 바로 짤 수 없다. 힘들다. 못한다. 그래서 1,2,3,4 순서를 거치는 것.  
for문을 짜면 필수적으로 저장소가 등장한다.
위의 acc 저장소의 스코프는 4줄이다. 재귀는 고작 한줄.
사람이 기억할 수 있는 저장소의 길이는 최대 7줄.
그래서 for문이 어렵다. for문은 무조건 저장소가 나오고 라이프사이클이 길다.

재귀로 짜고 for문으로 돌리는 연습을 해보자

## 과제: 1차원배열의 합 재귀, 꼬리재귀, for문 세개 코드 작성하기

#### 내가 짠 코드

```js
//재귀
const sum = (s, i) => (i > 0 ? s[i] + sum(s, i - 1) : s[0])

//꼬리물기
const taleSum = (s, i, acc) =>
  i > 0 ? taleSum(s, i - 1, acc + s[i]) : acc + s[0]

//for문
const arraySum = s => {
  let acc = 0
  for (let i = s.length - 1; i >= 0; i--) acc += s[i]
  return acc
}
```

#### 학생의 코드

```js
const recursive = (list, index = 0, acc = 0) => {
  if (!Array.isArray(list)) return 0
  if (typeof list[index] !== 'number') return acc
  return recursive(list, index, acc + list[index])
}
```

학생 코드리뷰 내용

#### 오류와 실패

오류는 있는데 실패하지 않는 경우 : 내부에 있는 함수나 기능이 \*내결함성을 갖는 경우

\*내결함성 : 오류가 발생했을 때 버티는 경우, 대부분 런타임에러가 안생기는 대신 컨텍스트 에러가 생긴다. 프로그램이 죽지는 않지만 결과가 엉뚱하게 나온다. 안정성은 있지만 신뢰성이 제거된다. 안정성과 신뢰성은 trade-off 관계.
무조건 신뢰성을 높여라. 안정성은 시니어가 된다면 신경써라.

프로그램에서 무서운 것은 컨텍스트 에러. 오류를 감추는 내결함성을 도입할 때는 신중해야 한다. 일반적인 경우 내결함성 도입은 무조건 좋지 않다. 내결합성을 갖추지 않고 오류를 표출하게끔 바꿔줘야 한다.

아래처럼 수정

```js
const recursive = (list, index = 0, acc = 0) => {
    if (!Array.isArray(list)) throw `invalid list ${list}`;
    if (typeof list[index]) !== 'number') throw `invalid element ${index}:${list[index]}`
    return recurseve(list, index, acc + list[index]);
};
// 결정을 사용자가 해야 함.
// throw 대신 return 0 등으로 쓰는 것은 사용자의 권리를 뺏는 것.
let result = 0;
try {
	result = recursive(17)
} catch(e) {
    result = -1
}
```

## 강의링크

https://youtu.be/0lAsf19iE2g
