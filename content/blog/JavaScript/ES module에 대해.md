---
title: 'ES module에 대해'
date: 2022-03-27
category: 'JavaScript'
draft: false
---

## module이 해결한 문제

코딩은 거의 변수를 다루는 문제라고 할 수 있다. 변수에 변수를 더하거나 할당하거나 등등.. 이렇기 때문에 변수를 잘 체계화하는 것이 개발과 유지보수에 큰 영향을 미친다.

스코프 덕분에 작은 단위로 변수를 바라보는 것이 가능해졌다. 한 함수를 짤 때 다른 함수의 변수를 고려하지 않아도 된다. 하지만 함수들끼리 변수를 공유할 수 없는 단점도 있다. 해결책으로 보통 함수들의 공통 스코프에 변수를 선언한다.

이럴 경우 단점들을 알아보자.

1.  그 공통스코프의 변수에 어떤 함수들이 의존하는지 알기 힘들다. 레거시 코드(공통스코프의 변수들)을 제거할 때 어느 함수가 에러를 낼 지 모르게 되어버리는 룰렛게임이 된다.

2.  어떤 함수가 공통스코프의 변수를 변경시켜버릴 수도 있다.

모듈은 이런 변수와 함수들을 체계화하는데 도움을 준다. 변수와 함수가 모듈 스코프 내에서 작동하게 한다. 그리고 명시적으로 export, import 를 한다. 이로 인해 공통스코프 없이 공유가 가능하고, 명시적이기 때문에 위의 1번 단점이 해결 가능하다.

모듈은 꽤 유용하기 때문에 그동안 자바스크립트에 모듈을 추가하려는 여러번의 노력이 있었다. 대표적으로 CommonJS(Node가 역사적으로 사용해온), ES modules가 있다.

## ES modules 의 동작원리

모듈과 함께 개발할 때, 의존성 그래프를 작성한다고 보면 된다. import를 통해 서로 다른 의존성 간의 연결이 이루어진다. 이 import로 브라우저나 노드는 어떤 코드를 load해야 하는지 안다. 그래프의 진입점으로 사용할 파일만 있으면 import를 따라가면서 다른 코드들을 찾는다.

그러나 브라우저가 파일 자체를 사용할 수는 없다. 파일을 `Module Records`라고 불리는 자료구조로 변환해야 한다. 그 후 Module record를 `Module instance`로 변환해야 한다. Module instance는 `코드`와 `상태`, 두가지를 결합한다.

- 코드 : 일련의 지시사항들이다. 레시피라고 할 수 있다.
- 재료 : 상태이다. 실제 값을 가지고 있다.

우리가 결국 필요한 것은 이 `Module instance` 이다. 모듈 로딩의 과정은 진입점 파일을 가지고 Module instances 들의 그래프를 만드는 것이다.

ES modules 은 위 과정을 3단계를 거쳐 수행한다.

1. Construction - 파일을 가져오고 / module records로 변환한다.
2. Instantiation - export 된 값들을 담을 공간을 메모리에서 찾는다. 그리고 import, export 모두 이 메모리 공간을 가리키도록 한다. 이 과정은 linking 이라고 한다.
3. Evaluation - 메모리 공간을 실제 값으로 채운다.

보통 사람들은 ES module이 비동기적이라고 한다. 작업이 세단계로 수행되고 각각 개별적으로 수행될 수 있기 때문에 비동기라고 생각할 수 있다. 하지만 반드시 비동기적일 필요는 없다. 1번 과정에서 로드 부분을 어떻게 하느냐에 따라 다르다.

(CommonJS는 1,2,3과정을 한번에 거친다.)

`ES module spec` 은 1단계 중 어떻게 파일을 `module records`로 변환하는지 그리고 2번째 3번째 단계들을 어떻게 수행하는지에 대해 설명하고 있다. 하지만 어떻게 파일을 가져오는지는 설명하고 있지 않다.

파일을 가져오는 것은 `로더`이다. 로더는 사용하는 플랫폼에 따라 달라진다. 브라우저에서는 `HTML spec` 을 따른다. 이 로더는 본인의 스펙(브라우저라면 HTML spec)에 맞는 타이밍에 ES module 메서드들을 호출한다. JS 엔진의 조종사와 다름없다. 각 단계가 어떻게 수행되는지 더 자세하게 알아보자

### Construction

1. 모듈이 포함된 파일의 다운로드 위치를 확인한다.
2. 파일을 가져온다.
3. 파일을 모듈 레코드로 변환한다.

로더가 위1, 2번 단계를 수행한다. 우선 처음 진입점 파일을 찾아야 하는데 HTML에서는 스크립트 태그로 진입점을 찾는다. 그 다음 import문의 `module specifier` 을 통해 다음 모듈을 찾는다.

```jsx
import { count } from './counter.js';
// './counter.js' 부분이 module specifier
```

파일을 컴파일하기 전까지는 이 파일이 다른 어떤 모듈에 의존하는지 알 수 없다. 모든 의존성 그래프를 한번에 그릴 수는 없다. 그렇다고 모든 모듈이 컴파일 될 때까지 main 쓰레드를 멈춘다면 앱 작동이 너무 늦을 것이다. 그래서 ES module spec은 위와 같이 3단계로 나누는 것이다.

\*CommonJS는 1,2,3 단계를 한번에 수행한다. module instance를 리턴하기 전에 로딩, instantiating, evaluating을 모두 수행하는 것이다. 이 때문에 module specifier에 변수 사용이 가능하다.

ES module은 module graph를 evaluation 이전에 그린다. 그래서 실제 값이 없는 상태이기 때문에 module specifier에 변수 사용이 불가능하다.

```jsx
✅ require (`${path}/counter.js`).count;
❌ import { count } from `${path}/counter.js`;
```

ES module에서 변수를 사용하고 싶다면 `dynamic import`라는 대안이 있다. dynamic import가 된 부분은 main과 별개의 새 그래프를 만든다.

### Parsing(변환)

파일을 가져왔으니 module record로 변환해야 한다. 이 과정은 브라우저가 모듈을 이해하는데 도움을 준다.

module record가 생성되면 `module map` 안에 배치된다.

html script태그에 type=’module’ 을 지정하면 브라우저는 이 파일이 모듈로 인식됨을 알 수 있다. 하지만 노드는 html태그가 없다. 그래서 .mjs extension 으로 해결하려는 시도가 있었다. 이 논의는 아직 진행중이다.

### Instantiation

우선 JS 엔진은 module record의 변수들을 다루는 `module environment record` 를 생성한다. module record마다 하나씩 생성된다. 그 후 이 module environment record는 모든 export들을 위해 메모리의 빈 공간을 찾아낸다. 그리고 메모리의 어떤 공간이 어떤 export와 연관되어 있는지 계속 추적한다. 공간에 실제 값 할당은 다음 단계인 evaluation 에서 이루어진다.

전체 export 를 모두 수행한 후 import 과정을 수행한다. import 과정은 메모리에서 해당 export가 저장된 공간에 linking한다.

(commonJS 는 export와 improt 가 각자 다른 메모리를 소비한다. 같은 메모리주소만 복사해서 각자 가지고 있다. 그렇기 때문에 exporting module에서 값을 바꿔도 importing module에서는 알 수 없다.)

### Evaluation

메모리 공간에 실제 값을 할당한다.

## 출처

https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/
