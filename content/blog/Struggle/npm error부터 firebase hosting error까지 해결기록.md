---
title: '[삽질기] npm error부터 firebase hosting error까지 해결기록'
date: 2021-02-16 15:00:00
category: 'Struggle'
draft: false
---

## 리액트로 넷플릭스클론앱을 만들고 있던 도중 나온 에러들과 해결기록 (window)

중간중간 에러가 날 때 node_modules 폴더를 삭제하고 `npm install` 하고 `npm run start`를 다시 하면 해결이 되었다.

나왔던 에러

- No render method found on the returned component instance
- code: 'MODULE_NOT_FOUND' ...

그런데 `npm install`을 할 때마다 deprecated warnings 들이 주루룩 뜨는게 신경쓰였다.
node를 아직 안배웠지만 아! 이건 업그레이드를 해주면 되나? 싶어서 node와 npm을 업그레이드 해봤다. 보니까 node를 설치하면 npm은 자동설치가 된다. **윈도우에서 node 업데이트 할 때는 그냥 공식홈에서 node 를 다시 설치**해주면 된다. 그러면 npm은?
알아보니까 npm update는 `npm i -g npm-upgrade`로 실행했다.

이제 최신버전을 가지고 기쁜 맘으로 코드를 이리저리 만지고 다시 넷플릭스클론앱에서
`npm run start`를 하는데 온갖 에러들이 튀어나왔다. 캡쳐를 했어야 했는데😥
이때는 내 코드가 잘못됐나 싶어서 계속 이리저리 바꿔보다가 "아니다 코드는 잘못된게 없는 것 같다. 뭐가 문제지?" 하다가 업그레이드한게 생각이 났다.

node의 안정적 버전이 아니라서 그런가? 싶어서 지우고 LTS 버전으로 설치했다.
혹시 몰라서 `npm i -g npm-upgrade`은 하지 않았다.
그랬더니..

## mime.lookup is not a function

에러가 떴다.
![](https://images.velog.io/images/yonyas/post/5d8f1dc2-665e-4814-8e44-320fc9bdae6b/mim.png)
찾아보니까 에러를 뿜는 곳, node*modules\send\index.js 에서 838줄과 845줄을 수정하면 된다.
*(참고: 2. mime.lookup 에러)\_

```js
mime.lookup(type)-- > mime.getType(type)
mime.charsets.lookup(type)-- > mime.getType(type)
```

이제 `npm run start`는 잘 되었다.

문제는 앱 완성 후 `npm build`를 하려 하는데..

## Cannot read property 'then' of undefined

에러가 떴다.
![](https://images.velog.io/images/yonyas/post/0ec9b948-8670-40ce-83e1-ff10537e388a/then.png)
분명 `npm run start`는 되는데 왜 같은 코드인데 `npm build`는 안될까? 뭔가 웹팩과 바벨 문제인가? 하지만 나는 create-react-app으로 만들었기 때문에 그리고 아직 웹팩 바벨을 만지는 엄두가 나지 않아서 다른 해결방법을 열심히 찾아보았다.
참고로 node_modules 폴더 삭제 방법은 더이상 먹히지 않았다.

현재 코드구조가 axios를 이용해서 movie api를 받고, 이 movie api와 movie-trailer npm 을 사용해서 youtube url을 받고, 영화예고편을 틀어주는 것이다. then, catch 를 처음 사용해보았는데 저기 있는 저 then 때문이구나! 싶어서 알아보니까 **youtube url을 받고 이를 바로 활용하지 말고 중간에 다른 함수를 써 넣어주면** 해결이 되는 것 같았다. _(참고: 3. then 에러1)_

그래도 계속 같은 에러가 났다.
찾아보니까 **async, await 을 쓰면 then을 안 써도 되는 것 같았다**. _(참고: 4. await 사용)_
좋아 then을 아예 없애보자! 하고 수정했지만 계속 똑같은 에러가 났다...
아니 then도 없는데 무슨 then을 읽지 못한다는거지?

그렇구나 내 코드에러가 아니라 node, npm 저것들의 문제겠구나.
그럼 npm 업데이트를 또 해볼까.. 했더니 도돌이표로 첫번째 에러가 또 생겼다.
다시 node LTS 버전 설치 후 고민했다. 캐쉬 문제인가 윈도우 문제인가.. 이래서 리눅스 쓰나..

좋아 그럼 npm 보다 yarn 이 좀더 좋다고 하는데 yarn 을 사용해보자.
`yarn build` 하니까 또 에러..
yarn은 업데이트 안해봤으니까 해봐야겠지 !
**`yarn upgrade` 하니까 뭔가 주루룩 설치되었고** !!

## 'react-scripts'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다.

에러가 드디어 바뀌었다. 😭
![](https://images.velog.io/images/yonyas/post/3394c637-f757-4d39-99aa-88f15f3402a9/react-script.png)
검색해보니 `yarn add react-scripts`를 해주면 되는 문제였다. _(참고: 5.react-scripts 에러)_

드디어 호스팅을 해보는구나 !! 싶었다.
`firebase init` `firbase deploy`를 하는데
![](https://images.velog.io/images/yonyas/post/5eedfa28-e93a-4160-b303-8da3188057b1/1Wv9O.png)
내 앱이 아니라 이 화면만 자꾸 보여줬다.
허참.
또 찾아보니까 `npm install -g firebase-tools`를 하면 된다고 한다. _(참고: 6.firebase-tools설치)_
나는 이제 yarn을 쓰기로 맘먹었는데 yarn 버전의 명령어는 찾다가 실패했다. 그냥 알려준대로 npm 으로 firebase-tools를 설치하고 다시 `firebase init`을 했다.

나중에 알게됐는데 `npm install -g firebase-tools` 명령어를 관리자모드에서 안치면 에러가 떴다. 처음 호스팅 할 때 저 명령어를 친것 같은데 호스팅한다고 흥분해가지고 에러뜬걸 못보고 지나간 것 같다.

쨋든 그랬더니.. 드디어 해결
주소를 누르니까 이쁜 넷플릭스 클론앱이 나왔다.😆

쨘 https://netflix-clone-83541.web.app/
![](https://images.velog.io/images/yonyas/post/f9d752ee-a176-4bc4-8571-f31d5e8ed830/%EC%9D%B4%EB%AF%B8%EC%A7%80%203.png)

<br><br><br><br><br><br>

## 참고

1. 넷플릭스클론 튜토리얼 https://www.youtube.com/watch?v=XtMThy8QKqU
2. mime.lookup 에러 https://github.com/smogon/pokemon-showdown/issues/4134
3. then 에러1 https://study-develop.tistory.com/2
4. await 사용 https://ko.javascript.info/async-await
5. react-scripts 에러 https://thespoiler.tistory.com/21
6. firebase-tools 설치 https://blog.roto.codes/deploy-react-app-to-firebase/
