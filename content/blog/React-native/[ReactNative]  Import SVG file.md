---
title: '[ReactNative] Import SVG file'
date: 2021-06-19
category: 'React-native'
draft: false
---

## svg란?

- Scalable Vector Graphics
- \*XML 형식의 vector 기반 그래픽이다.
- XML 형식이라 CSS 와 Javascipt 로 수정 가능

#### \*XML형식이란?

Extensible Markup Language
데이터를 기술하고 구조화하는 것과 관련한 기술
데이터의 교환을 위해 고안된 기술

예를 들어 고양이를 설명하는 데이터를 보면

```xml
 <?xml version="1.0"?>
 <CAT>
   <NAME>Izzy</NAME>
   <BREED>Siamese</BREED>
   <AGE>6</AGE>
   <ALTERED>yes</ALTERED>
   <DECLAWED>no</DECLAWED>
   <LICENSE>Izz138bod</LICENSE>
   <OWNER>Colin Wilcox</OWNER>
 </CAT>
```

위 구조로 고양이의 이름, 연령 등을 쉽게 찾을 수 있다. 해당 파일을 HTML파일에 붙여넣고 웹에서 볼 경우는 아래와 같이 데이터만 표시된다.

```xml
 Izzy Siamese 6 yes no Izz138bod Colin Wilcox
```

## svg 파일 쓰는 이유

- \*vector 기반이라서 깨지지 않는다.  
  \* vector란? 비트맵(래스터)과 다른 이미지 표현 방식으로써 점과 점을 연결해 표현하는 방식
- 용량이 작다.(브라우저 로딩속도가 빨라진다!)
- 수정이 가능하다.
- 애니메이션을 입힐 수 있다.
- \*W3C의 추천  
  \* W3C란? 웹표준을 개발하는 국제 컨소시엄

## Import 방법

1. npm 설치 `expo install react-native-svg`
2. assets 폴더에 svg 파일을 넣는다.
3. 루트폴더나 utils 등 폴더에 svg를 사용할 .js 파일을 만든다. 예제파일이름은 svg.js
4. svg.js 내용

```js
import React from 'react'
import { SvgXml } from 'react-native-svg'

const logo = `
<svg width="72" height="37" viewBox="0 0 72 37" fill="none" xmlns="http://www.w3.org/2000/svg">
    <rect x="36.5355" width="20" height="5" rx="2.5" transform="rotate(45 36.5355 0)" fill="#70AFAF" fill-opacity="0.85"/>
    <rect x="33" y="25.1422" width="20" height="5" rx="2.5" transform="rotate(-45 33 25.1422)" fill="#85B2D2"/>
    <rect x="36.1421" y="28.6777" width="20" height="5" rx="2.5" transform="rotate(-135 36.1421 28.6777)" fill="#70AF7E" fill-opacity="0.85"/>
    <rect x="22" y="14.1422" width="20" height="5" rx="2.5" transform="rotate(-45 22 14.1422)" fill="#DFA2A2"/>
</svg>
`

const svg = () => {
  const LogoSvg = () => <SvgXml xml={logo} width="30%" height="30%" />
  return <LogoSvg />
}

export default svg
```

5. 사용할 파일에서

```js
import LogoSvg from '../utils/svg'
;<View>
  <LogoSvg style={{ color: 'red' }} />
</View>
```

## 참고

1. [SVG를 사용하는 이유](https://brunch.co.kr/@ggk234/11)
2. [초보자를 위한 XML 설명](https://support.microsoft.com/ko-kr/topic/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-xml-%EC%84%A4%EB%AA%85-a87d234d-4c2e-4409-9cbc-45e4eb857d44)
3. [How to use SVGs in React Native with Expo](https://levelup.gitconnected.com/how-to-use-svgs-in-react-native-with-expo-ec34f085f5e0)
