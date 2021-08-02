---
title: '[ReactNative expo] 시작할 때 알면 좋은 것들(에러해결기)'
date: 2021-06-08
category: 'React-native'
draft: false
---

리액트 네이티브 처음 사용시 초기세팅을 마치고 앱 띄울 때 알면 좋은 점들 정리

### JAVA version

우선 초기세팅에서 중요한 점은 **JAVA8(jdk)** 버전을 설치하는 것.
최신버전을 설치했다가 왜안되지 하고 한참을 헤맸다.

> JDK = JRE + @, 읽기/쓰기 전용
> JRE는 읽기 전용이다.

### 앱띄우기 순서

1. expo init [파일명]
2. cd [파일명]
3. npm start (인터넷 새 창에 Metro Bundler가 켜진다.)
4. Android Studio를 켜고 에뮬레이터를 run 한다.
5. 아까 했던 npm start 터미널에서 a 를 눌러서 open Android
6. Metro Bundler 에서 'Run on Android device/emulator' 클릭

**꼭 에뮬레이터가 켜진 후 a 를 눌러야 한다.**
**QR코드로 실행하려면 device와 노트북이 같은 와이파이에 연결되어 있어야 한다**

### 연결 끊기는 문제

중간중간에 시도때도 없이 갑자기 연결이 끊긴다. 에뮬레이터 끄고 ctrl+C 로 npm 연결 끊고 재시작.
아니면 npm start 한 곳에서 `r` 누르면 refresh 된다.

### Android studio loading device 문제

AVD manager 에서 에뮬레이터 실행해야 하는데 loading device 만 떠있고 실행이 되지 않을 때
방법 1.
Android studio 관리자모드로 실행(실패)
방법 2.
Android studio 끄고 task manager에서 adb 없애고 재실행(성공)

### 에뮬레이터 실행시 'adb not found' error

무시해도 된다.

### 개발자모드 안들어가질 때

윈도우는 Ctrl+M 을 누르면 개발자 모드로 들어가진다.
이 때 Ctrl+M 이 아예 안 먹는 경우가 있다.
이럴 때는

1. AVD manager 에서 플레이스토어가 있는 버전을 다운받는다.
2. 기존의 Expo go 어플을 지우고 플레이스토어에서 다운받는다.

### prettier 적용 안되는 문제

익스텐션만 설치한다고 바로 적용이 안된다.

```js
npm i -D prettier
```

#### eslint는 ?

```js
npm i -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-universe

// package.json
{
  "eslintConfig": {
    // for expo projects using react native
    "extends": "universe/native"
  }
}

// .eslintrc.js
module.exports = {
  extends: "universe/native",
};
```

### npm install 하다가 npm ERR! errno -4048

VSC 끄고 다시 시작. metro bundler도 끄고 다시 시작.
다시 npm install

### 출처

https://decodenatura.com/set-up-react-native-typescript-eslint-prettier/
