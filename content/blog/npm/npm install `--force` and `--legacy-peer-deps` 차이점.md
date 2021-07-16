---
title: 'npm install `--force` and `--legacy-peer-deps` 차이점'
date: 2021-07-11
category: 'npm'
draft: false
---

## 에러의 시작

![](https://images.velog.io/images/yonyas/post/3622df09-d3b9-4ca8-a42f-4a0ff4b4123c/first_error.png)

`npm install`을 하는데 "unable to resolve dependency tree" 에러가 떴다.
에러를 읽어보니
katex@0.11.1이 현재 노드모듈에 있는데,
^0.10.0이 설치되어야 버전에러가 뜨지 않는다.

그래서 katex를 다른 버전으로 다시 설치하려 했는데
스택오버플로어를 보니 `npm install --force` or `npm install --legacy-peer-deps`를 하면 된다고 한다.
실제로도 해결되었다.

저 둘의 차이점은 무엇일까?

## npm 7버전

21.2월에 npm 7버전이 나왔다.
중요한 변화는 아래와 같다.

> Automatically installing peer dependencies is an exciting new feature introduced in npm 7. In previous versions of npm (4-6), peer dependencies conflicts presented a warning that versions were not compatible, but would still install dependencies without an error. npm 7 will block installations if an upstream dependency conflict is present that cannot be automatically resolved. **- in npm github blog**

요약: npm 4-6버전에서는 peer dependencies 가 있으면 경고만 뜨고 설치는 되었다. 7버전은 설치가 막힌다.

## 차이점

> You have the option to retry with --force to bypass the conflict or --legacy-peer-deps command to ignore peer dependencies entirely (this behavior is similar to versions 4-6). **- in npm github blog**

요약: --force로 충돌을 우회하든지 --legacy-peer-deps로 충돌을 무시하든지(npm 4-6버전과 비슷한 방식) 선택하세요.

- --force를 하면 package-lock.json에 몇가지의 다른 의존 버전들을 추가한다.
- --legacy를 하면 peerDependency가 맞지 않아도 일단 설치한다.

일단 --force를 해서 다른 의존버전들을 추가하고 실행해보고 안되면
--legacy를 해서 일단 오류없이 설치한 후 실행해보는 게 좋은 것 같다.

## 참고

1. https://stackoverflow.com/questions/66020820/npm-when-to-use-force-and-legacy-peer-deps
2. https://github.blog/2021-02-02-npm-7-is-now-generally-available/
