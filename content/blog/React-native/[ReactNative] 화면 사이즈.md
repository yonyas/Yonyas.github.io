---
title: '[ReactNative] 화면 사이즈'
date: 2021-06-09
category: 'React-native'
draft: false
---

![](https://images.velog.io/images/yonyas/post/bda34a4c-2448-4aa4-995e-edf92bad78e7/RNlogo.png)

## Dimensions API

```js
import { Dimensions } from 'react-native'

console.log(Dimensions.get('screen'))
// 전체 화면 사이즈 알 수 있다.
```

Dimensions.get() 안에는 window or screen 입력 가능

> 'window' : 현재 화면 사이즈
> 'screen' : 전체 화면 사이즈
> ios 는 둘이 같은데 android는 window가 screen보다 살짝 작다.

## Rotate

처음에는 rotate 반영 안됨.
`app.json` 보면 `orientation: portrait` 로 되어있음.
portrait를 default로 변경하면 portrain, landscape 둘다 지원.
수정 후 저장.

## react-native-community/hooks API

### useDimension 사용법

화면 rotate 할 때 width 알아보려면

```
yarn install @react-native-community/hooks

import { useDimensions } from "@react-native-community/hooks";

console.log(useDimensions());
```

npm 으로 깔면 에러나니까 yarn으로 하기

### useDeviceOrientation 사용법

@react-native-community/hooks의 API 중 useDeviceOrientation 은 현재 방향 알 수 있다.

```js
import { useDeviceOrientaion } from "@react-native-community/hooks";

export default function App() {
  const { landscape } = useDeviceOrientation();

  return(
    <View
    	style={{
    		height: landscape ? "100%" : "30%"
        }}
    ></View>
}
```

```js
// console.log(useDeviceOrientation()) 하면 아래같이 나옴
{ landscape: true, portrait: false}
```
