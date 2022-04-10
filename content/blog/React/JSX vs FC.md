---
title: 'JSX vs FC (React.FC 의 장단점)'
date: 2022-04-10
category: 'React'
draft: false
---

TypeScript는 리액트 컴포넌트를 JSX.Element로 타입추론을 한다. 컴포넌트 타입을 `React.FC`로 하려면 타입을 명시해주어야 한다. 현재는 FC로 명시해서 사용하고 있는데 굳이 명시를 계속 해줘야 하는지 JSX와 차이는 무엇인지 궁금해서 찾아보았다.
`React.FC` 사용의 장단점을 알아보겠다.

#### React.FC 의 장점

1. 컴포넌트에 `displayName`을 명시할 수 있다. displayName은 UI 라이브러리를 사용할 때 dev tool에서 컴포넌트명 확인을 할 수 있다.

2. 올바른 리턴타입을 강제한다. 만약 실수로 false를 리턴한다면 에러가 난다.

#### React.FC 의 단점

1. children을 내포하고 있다. 그렇기 때문에 children을 props로 받지 않는 컴포넌트에 children을 주어도 에러가 생기지 않는다.

```ts
const NoChildren: React.FC = () => {
	<div>NoChildren component</div>
}

function Container() {
	return <NoChildren> HI </NoCHildren>
}
```

2. 코드 가독성을 해친다.

```ts
const NoChildren: React.FC<NoChildrenProps> = ({
  item: number,
}: NoChildrenProps) => {
  <div>NoChildren component</div>;
};
```

추론하게 둔다면 아래와 같이 간단해진다.

```ts
const NoChildren = ({ item: number }: NoChildrenProps) => {
  <div>NoChildren component</div>;
};
```

2-1. Copound components 의 가독성을 해친다.

```ts
<Select>
  <Select.Item>Item 1</Select.Item>
</Select>
```

`React.FC` 로는 아래와 같이 사용해야 한다.

```ts
const Select: React.FC<SelectProps> & { Item: React.FC<ItemProps> } = props => {
  /* ... */
};
Select.Item = props => {
  /*...*/
};
```

`React.FC` 를 사용하지 않는다면 아래와 같이 사용할 수 있다.

```ts
const Select = (props: SelectProps) => {
  /* ... */
};
Select.Item = (props: ItemProps) => {
  /*...*/
};
```

3. defaultProps 문제
   아래 코드의 ComponentProps에서 name이 optional parameter 이지만 defaultProps가 지정되어 있으므로 `name.toUpperCase()` 에서 undefined 타입에러가 나지 않아야 할 것 같다. 하지만 `React.FC` 컴포넌트에서는 에러가 난다. JSX에서는 타입에러 없이 잘 작동한다.

```ts
type ComponentProps = { name?: string }; // optional

const Component: React.FC = ({ name }: ComponentProps) => (
  <div>{name.toUpperCase()} // ❌ error</div>
);

Component.defaultProps = { name: 'John' };

const Example = () => <Component />;
```

## 참고

1. https://github.com/facebook/create-react-app/pull/8177
2. [Why you probably shouldn’t use React.FC to type your React components | by Sam Hendrickx | Raccoons Group | Medium](https://medium.com/raccoons-group/why-you-probably-shouldnt-use-react-fc-to-type-your-react-components-37ca1243dd13)
