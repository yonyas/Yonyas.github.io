---
title: '[프로그래머스] Jaden Case - JS'
date: 2021-09-06
category: 'Algorithm'
draft: false
---

## 문제

#### 문제 설명

JadenCase란 모든 단어의 첫 문자가 대문자이고, 그 외의 알파벳은 소문자인 문자열입니다. 문자열 s가 주어졌을 때, s를 JadenCase로 바꾼 문자열을 리턴하는 함수, solution을 완성해주세요.

#### 제한 조건

s는 길이 1 이상인 문자열입니다.
s는 알파벳과 공백문자(" ")로 이루어져 있습니다.
첫 문자가 영문이 아닐때에는 이어지는 영문은 소문자로 씁니다. ( 첫번째 입출력 예 참고 )

#### 입출력 예

"3people unFollowed me" => "3people Unfollowed Me"
"for the last week" => "For The Last Week"

## 내 풀이

먼저 문자를 split를 이용해서 배열로 만들고 map으로 돌면서 제일 앞문자만 대문자로 바꾼다.

```js
'abc cc'
  .split(' ')
  .map(v => {
    v = v.split('');
    v[0] = v[0].toUpperCase();
    return v.join('');
  })
  .join(' ');
```

처음에는 아래와 같이 했다가 실패했다.  
새 배열을 만들고 앞자리만 대문자로 바꾼 후 리턴해줬다. 그런데 계속 원래의 'abc cc'가 나왔다.

```js
//실패 코드
'abc cc'
  .split(' ')
  .map(v => {
    let arr = v.split('');
    arr[0].toUpperCase();
    return arr.join('');
  })
  .join(' ');

//'abc cc'
```

왜 저렇게 생각했냐면

```js
let someArr = ['a', 'b'];
someArr[0] = 1;
console.log(someArr); // [1, 'b']
```

이렇게 하면 원하는대로 잘 나오기 때문이다. arr를 지우고 v를 사용하니까 해결됐는데 이유를 명확히 모르겠다..

문제를 잘 보면 첫문자를 대문자로 바꾸는것 뿐만 아니라 나머지문자들을 소문자로 바꿔줘야 한다. 아래 코드 추가해서 완성

```js
//완성 코드
'abc cCC'.split(' ').map(v => {
  v = v.slice(0, 1) + v.slice(1).toLowerCase();
  return v;
});

'abc cc'
  .split(' ')
  .map(v => {
    v = v.split('');
    v[0] = v[0].toUpperCase();
    return v.join('');
  })
  .join(' ');
```
