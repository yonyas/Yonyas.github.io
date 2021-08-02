---
title: '[CSS] Img태그를 이용해서 이미지가 항상 center를 보여주도록 반응형 배경 만들기'
date: 2021-03-05
category: 'CSS'
draft: false
---

## 하려는 것

아마존 클론을 하고 있다.
아마존 배경화면을 보면 화면을 줄일 때 항상 이미지의 가운데 부분을 보여준다.

<img src="https://images.velog.io/images/yonyas/post/febaeadb-90da-494d-8d72-44c467860ad6/full.png" width="500px">
이미지보다 화면이 작아지면 왼쪽 오른쪽 부분이 짤리면서 가운데만 보여준다. 
<img src="https://images.velog.io/images/yonyas/post/d82c2f1d-57d4-4b17-953c-9c90e540dc95/reduce.png" width="500px">
 화면을 더 줄이면 어느 순간부터는 이미지는 왼쪽으로 고정이 되고 오른쪽 부분만 짤리면서 축소된다.
<img src="https://images.velog.io/images/yonyas/post/7b33389e-50aa-4b49-8f8e-31a01f565f61/small.png" width="500px">

보통 이미지를 화면 중앙에 넣을 때 margin: 0 auto; 를 사용한다.

```css
img {
  display: block;
  margin: 0 auto;
}
```

이렇게 하면 화면을 줄일 때 이미지의 가운데 부분만 보여주는게 안 된다. 왼쪽으로 고정되고 오른쪽 부분이 짤리게 된다. 위 3번째 그림처럼.

## 부모 div 두개로 img태그 감싸기

html 코드

```html
      <div className="img-size">                   <!-- 1번(red) == 화면크기 -->
        <div className="img-center">               <!-- 2번(blue) -->
            <img className="img"src="url"></img>   <!-- 3번(green) -->
        </div>
      </div>
```

css 코드

```css
.img-size {
  position: relative;
  width: 100%;
  height: 600px;
}
.img-center {
  text-align: center;
  position: absolute;
  top: 0;
  right: -200%;
  bottom: 0;
  left: -200%;
}
.img {
  max-width: 100%;
  max-height: 100%;
}
```

<img src="https://images.velog.io/images/yonyas/post/ae6dcfff-10ad-4ac7-8fe4-17fdb0676d3d/img-center.png" width="250px">

1번-red 태그는 width: 100%; 로 화면너비와 같다.
2번-blue 태그는 1번태그를 기준으로 position: absolute; 되어 있다.
위아래는 0으로 착 붙어있고 좌우는 -200%을 줘서 현재 보이는 화면보다 더 큰 박스가 만들어져 있다.
text-align: center;를 줬기 때문에 이미지는 항상 2번-blue태그 안에 가운데 위치한다.

### 그림설명

a는 현재 이미지가 화면 가운데 이쁘게 있다.
점점 화면(1번-red태그)를 축소할 때를 그림으로 그려봤다.
e처럼 그림이 올라가면 안된다. 그래서 c 쯤에서 이미지를 왼쪽으로 고정시키고 오른쪽부분만 짤려서 보여주게끔 해야 한다.

```css
@media screen and (max-width: 1000px) {
  .img-center {
    text-align: left;
    left: -255px;
  }
}
```

나는 1000px를 기준으로 했다. 화면이 1000px 보다 작아지면 그림이 왼쪽으로 착 붙는다.
왼쪽부분을 고정시키기 위해 text-align: left;를 하고 left의 픽셀은 개발자모드를 통해 자연스러운 부분을 찾아냈다.
