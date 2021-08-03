---
title: '[삽질기] npm install error 해결기록'
date: 2021-08-02
category: '삽질기'
draft: false
---

맥을 사고 gatsby로 만들었던 깃헙블로그를 클론받았다.
에러가 떠서 에러해결기를 써본다.

### npm install 멈춤

`npm install`을 했는데 에러가 뜬다.
`npm install --force`와 `npm install —legacy-peer-deps`를 했는데 중간에 npm install 작동이 멈춘다.

<img width="818" alt="스크린샷 2021-08-02 오전 11 24 42" src="https://user-images.githubusercontent.com/60434382/127805974-364c6ffd-cecc-440d-b9d0-35cbc819eb81.png">

위 상태에서 멈춘다.

### npm uninstall katex

katex의 문제로 보여 uninstall해도 중간에서 멈춘다.
[구글링](https://stackoverflow.com/questions/16873973/npm-install-hangs)해보니 `npm install --verbose`해서 오류를 찾아보라 한다.
<img width="754" alt="스크린샷 2021-08-02 오전 11 16 36" src="https://user-images.githubusercontent.com/60434382/127806030-1e7b6e28-60b4-4a62-995f-d96204dcff03.png">

dependency 오류이고 이 해결법이 force와 legacy-peer-deps인데 ?
다른 [스택오버플로어](https://stackoverflow.com/questions/c/npm-install-error-rollbackfailedoptional/54173142#54173142) 답변을 적용해봤다.

### npm config set registry

<img width="663" alt="스크린샷 2021-08-02 오전 11 42 52" src="https://user-images.githubusercontent.com/60434382/127805743-3215452d-ad59-40ed-aab0-b80a7d4e973d.png">

이렇게 하니까 중간에 멈추는 현상이 해결됐다.
_The reason this helps is that somehow, your registry settings got set to something other than the default, and these commands remove those config settings, allow them to revert to their default values._

이라고 한다. 나는 proxy를 건든적이 없는데 .. 쨋든 한단계 앞으로 진전!
`npm install`을 했더니 이제 아래 에러가 떴다.

### command sh -c

<img width="900" alt="스크린샷 2021-08-02 오전 11 45 38" src="https://user-images.githubusercontent.com/60434382/127806142-50685b8c-560f-4ba8-a320-d61f0f01150d.png">

위 에러가 뜨는데 `sh -c`적용 전에 위에 katex를 지워보고 싶었다.
`npm uninstall katex`하니까 중간에서 또 멈춘다. `npm install` 도 또 멈춘다.

### node, npm version <— 원인

node 와 npm 버전이 맞지 않으면 문제가 될 수 있다고 한다.
혹은 내가 설치한 node 버전이 너무 최신일 수도 있다는 생각이 들었다.

node LTS version 인 14.17.4로 바꿔봐야겠다.
npm은 그에 맞는 버전인 6.14.4로 변경했다.

> node : v14.17.4
> npm : v6.14.4

<img width="902" alt="스크린샷 2021-08-02 오후 1 13 56" src="https://user-images.githubusercontent.com/60434382/127806177-814767bb-e66f-45bc-8c74-7723affb2a14.png">

이런 찜찜한 에러와 함께 쨋든 에러가 **해결**되었다.

### 로딩에러

![Aug-02-2021 13-35-18](https://user-images.githubusercontent.com/60434382/127806328-e21e4fa5-fc84-457e-bf96-0eb6d178b800.gif)

위와 같이 로딩에러가 났다. 프로필 부분이 안뜨는듯 하다.
<img width="906" alt="스크린샷 2021-08-02 오후 1 36 37" src="https://user-images.githubusercontent.com/60434382/127806222-19d82b9c-3958-4703-b4a0-68357eaca392.png">

블로그 스타터를 만들어 준 분께서 어떤 새 기능을 수행하고자 하는데 내 블로그가 당첨이 됐나보다!  
이 문제 때문인지 아니면 npm 문제 때문인지 모르기 때문에 일단 FLAG: false; 를 하려고 보니 gatsby-config-js에 FLAG가 보이지 않는다.

### npm run deploy

백엔드에서 서버를 가져올 때 뭔가 엉킨 것 같았다. 그래서 `npm start`말고 `npm run deploy`를 하니까 잘 실행되었다.
끝.
