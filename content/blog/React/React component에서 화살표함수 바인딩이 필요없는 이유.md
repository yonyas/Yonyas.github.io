---
title: 'React component에서 화살표함수는 바인딩이 필요없는 이유'
date: 2021-10-23
category: 'React'
draft: false
---

먼저 bind 를 살펴보자.
전역변수를 지정하고 시작

```js
function foo() {
  console.log(this.name);
}
var man = { name: 'jack' };
```

### 1 - 기본적인 bind

```js
foo = foo.bind(man); //foo 함수의 this를 man에 bind
foo(); // 'jack'
```

### 2 - man 프로퍼티에 지정

```js
man.foo = foo; // foo 함수의 this가 man을 바라보게 된다.
man.foo(); // 'jack'
```

### 3 - man.foo를 다른 변수에 지정

```js
//일반함수는 다른 변수에 할당하는 순간 this를 잃어버린다.
var bar = man.foo;
bar(); // undefined

//bind 해줘야 man 과 연결
bar = bar.bind(man);
bar(); // 'jack'
```

### 4 - `foo()`가 화살표 함수라면?

```js
const foo = () => {
  console.log(this.name);
};

//보통 this 는 호출한 형태에 따라 결정된다고 하는데 그건 일반함수의 경우이다. man.foo() 이면 man 이 this 에 바인딩 되어야 하는 것 같지만 아래와 같이 예상대로 되지 않았다.
man.foo = foo;
man.foo(); // undefined

//bind를 명시적으로 지정해줘도 바인딩 되지 않는다.
foo.bind(man);
man.foo(); // undefined
```

**이유 : arrow function 에 rebind할 수 없다. 화살표함수의 this는 정의된 컨텍스트와 함께 호출된다.**

## 호출할 때 this 를 잃어버리는 개념이 React component 만들 때 쓰인다.

- React 클래스형 컴포넌트에서 함수를 만들 때 일반함수로 만든다면 아래와 같이 this 바인드를 해줘야 한다.

```js
class Foo extends Component {
  constructor(props) {
    this.cb1 = this.cb1.bind(this); // bind !!!
    this.cb2 = this.cb2.bind(this);
    this.cb3 = this.cb3.bind(this);
  }
  cb1() {}
  cb2() {}
  cb3() {}
  render() {
    // 할당될 때 bind 를 잃는다.
    <Button onClick={this.cb1}>click me</Button>;
  }
}
```

`console.dir(Foo)`를 해보면 `cb1`함수는 `Foo` 클래스의 프로토타입 프로퍼티에 할당되어 있다.

- 화살표 함수로 만든다면 바인딩할 필요가 없다. - 이유 : 화살표 함수 cb1은 class Foo 컨텍스트 안에서 생성됐기 때문

```js
class Foo extends Component {
  constructor(props) {}
  cb1 = () => {};
  cb2 = () => {};
  cb3 = () => {};
  render() {
    <Button onClick={this.cb1}>click me</Button>;
  }
}
```

`console.dir(Foo)`를 해보면 `cb1`화살표 함수는 `Foo` 클래스가 아니라 만약 인스턴스를 만든다면 그 인스턴스의 프로퍼티에 할당되어 있다.

### 출처

[Arrow functions are disrupting React.Components | by Karthik Kalyanaraman | Medium](https://karthikkalyanaraman.medium.com/arrow-functions-are-disrupting-react-components-63662d35f97b)
