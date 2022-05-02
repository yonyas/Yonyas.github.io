---
title: '[Book] 이펙티브 타입스크립트 : 매핑된 타입을 사용하여 값을 동기화하기'
date: 2022-05-02
category: 'TypeScript'
draft: false
---

> 이펙티브 타입스크립트 아이템 18 부분을 읽으면서 정리한 내용입니다.

<br />
산점도(scatter plot)을 그리기 위해 UI컴포넌트를 작성한다고 해보자.

불필요한 렌더링을 막기 위해 필요할 때만 차트를 다시 그릴 것이다. 아래 props중 onClick 이벤트를 제외하고만 다시 그리면 된다.

```ts
interface ScatterProps {
  xs: number[];
  ys: number[];

  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
```

최적화를 두가지 방법으로 구현해 보겠다. (두가지 방법 모두 이상적이지 않은 방법이다)

### 1. 첫번째 방법 : 보수적 접근법, 실패에 닫힌 접근법

- 만약 zs, zRange 가 추가된다면 아래 두 조건문을 모두 통과하기 때문에(onClick이 아니니까) 계속 재렌더링이 일어날 것이다.

```ts
function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  let k: keyof ScatterProps;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k]) {
      if (k !== 'onClick') return true;
    }
  }
  return false;
}
```

### 2. 두번째 방법 : 실패에 열린 접근법

- zs나 zRange가 추가되어도 `false` 기 때문에 재렌더는 일어나지 않는다. 그러나 이 역시 좋은 방법은 아닌데 그 이유는 zs나 zRagne 가 추가될 때마다 누락하지 않고 입력해줘야 하는데 놓칠 수도 있기 때문이다.

```ts
function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  return (
    oldProps.xs !== newProps.xs ||
    oldProps.ys !== newProps.ys ||
    oldProps.xRange !== newProps.xRange ||
    oldProps.yRange !== newProps.yRange ||
    oldProps.color !== newProps.color
    // (no check for onClick)
  );
}
```

## 이상적인 방법은 ??

새로운 속성이 추가될 때마다 사용자가 직접 shouldUpdate를 고치도록 하는게 좋다.

바로바로 매핑된 타입과 객체를 사용하는 것이다.

```ts
const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
  // 여기 scatterProps
  xs: true,
  ys: true,
  xRange: true,
  yRange: true,
  color: true,
  onClick: false,
};

function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  // 여기 scatterProps
  let k: keyof ScatterProps;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k] && REQUIRES_UPDATE[k]) {
      return true;
    }
  }
  return false;
}
```

- 체크한 두 곳을 보면 같은 타입을 공유하고 있기 때문에 하나를 변경하면 다른 하나도 변경해야 함을 알 수 있다.
- 매핑된 타입은 한 객체가 또 다른 객체와 정확히 같은 속성을 가지게 할 때 이상적이다.
