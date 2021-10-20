---
title: 'SWR에 대해'
date: 2021-10-20
category: 'React'
draft: false
---

SWR은 data fetching React hook이다. 막강한 캐싱 기능이 있다. {data, error} 로 data와 error를 받아온다.

- swrConfig 로 기본옵션 설정

```jsx
<SWRConfig
  value={{
    refreshInterval: 3000,
    fetcher: (resource, init) => fetch(resource, init).then(res => res.json()),
  }}
>
  <Component />
</SWRConfig>
```

- 기본 사용

```jsx
const fetcher = url => axios.get(url).then(res => res.data);

function App() {
  const { data, error } = useSWR('/api/data/1', fetcher);
}
```

- 조건부 fetch 가능

```jsx
const { data } = useSWR(shouldFetch ? '/api/data' : null, fetcher);
```

- 의존성 가져오기

user 데이터가 두번째 코드줄에 반영

```jsx
const { data: user } = useSWR('/api/user');
const { data: projects } = useSWR(() => '/api/projects?uid=' + user.id);
```

### 인자

```jsx
// 아래 세 줄은 동일한 코드
useSWR('/api/user', () => fetcher('/api/user'));
useSWR('/api/user', url => fetcher(url));
useSWR('/api/user', fetcher);

// 조건부 사용 예
const { data } = useSWR(`/api/projects/${id}`, (url: string) => {
  if (id) return api.client.get(url);
});
```

- 다중인자

```jsx
// token 이 변경되면 다른 데이터 가져온다.
const { data: user } = useSWR(['/api/user', **token**], fetchWithToken)
```

- 캐슁

만약 두개의 컴포넌트에서 아래 코드를 중복되게 작성했다고 하자. 네트워크 탭을 보면 한번밖에 요청하지 않는다. 왜냐하면 해당 api 값을 기준으로 캐슁을 하기 때문 → 리소스 사용 효율적, 부모에서 받아서 내려줄 필요가 없다.

```jsx
const { data } = useSWR('/api');
```

### Mutate

mutate(key) 를 사용하면 해당 key를 사용하는 SWR hook 들의 값을 갱신할 수 있다.

예 ) useSWR(key, fetcher) 의 key를 말함.

```jsx
// 로그아웃 버튼 누를 때 Profile 정보 갱신하는 코드
import { useSWRConfig } from 'swr';

function App() {
  const { mutate }  = useSWRConfig()

  return (
    <>
      <Profile />              // 2. 갱신
      <button onClick={() => { // 1. 로그아웃 버튼
        //쿠키 만료 코드 ..
        mutate('api/user')
      }}
    </>
  )
}
```

3번째 인자에 false : POST 후 로컬 데이터는 바로 업데이트 하지만 갱신은 비활성화

장점 ) 해당 값이 post 된 후 다시 받아올 때까지 기다릴 필요가 없다. 빨라짐.

```jsx
import useSWR, { useSWRConfig } from 'swr';

function Profile() {
  const { mutate } = useSWRConfig();
  const { data } = useSWR('/api/user', fetcher);

  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button
        onClick={async () => {
          const newName = data.name.toUpperCase();

          // 로컬 데이터를 즉시 업데이트하지만, 갱신은 비활성화
          mutate('/api/user', { ...data, name: newName }, false);

          // 소스 업데이트를 위해 API로 요청 전송
          await requestUpdateUsername(newName);

          // 로컬 데이터가 올바른지 확인하기 위해 갱신(refetch) 트리거
          mutate('/api/user');
        }}
      >
        Uppercase my name!
      </button>
    </div>
  );
}
```

- mutate 로부터 데이터를 반환받을 수 있음

```jsx
try {
  const user = await mutate('/api/user', updateUser(newUser));
} catch (error) {
  // 여기에서 user를 업데이트하는 동안 발생하는 에러를 처리
}
```

- mutate 이 useSWR에 의해 생성되었다면 mutate 함수 사용시 key 파라미터 필요 없다

```jsx
import useSWR from 'swr';

function Profile() {
  const { data, mutate } = useSWR('/api/user', fetcher);

  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button
        onClick={async () => {
          const newName = data.name.toUpperCase();
          // 데이터를 업데이트하기 위해 API로 요청을 전송
          await requestUpdateUsername(newName);
          // 로컬 데이터를 즉시 업데이트하고 갱신(refetch)
          // 노트: 미리 바인딩 되었으므로 useSWR의 뮤테이트를 사용할 때는 key가 요구되지 않음
          mutate({ ...data, name: newName });
        }}
      >
        Uppercase my name!
      </button>
    </div>
  );
}
```

### Pagination

- Pagination - useSWR 사용

```jsx
function Page({ index }) {
  const { data } = useSWR(`/api/data?page=${index}`, fetcher);
  // ... 로딩 및 에러 상태를 처리
  return data.map(item => <div key={item.id}>{item.name}</div>);
}

function App() {
  const [pageIndex, setPageIndex] = useState(0);

  return (
    <div>
      <Page index={pageIndex} />
      /* 다음 페이지를 프리로드 한다. */
      <div style={{ display: 'none' }}>
        <Page index={pageIndex + 1} />
      </div>
      <button onClick={() => setPageIndex(pageIndex - 1)}>Previous</button>
      <button onClick={() => setPageIndex(pageIndex + 1)}>Next</button>
    </div>
  );
}
```

- 인피니트 로딩 - useSWR 사용

```jsx
function App() {
  const [cnt, setCnt] = useState(1);

  const pages = [];
  for (let i = 0; i < cnt; i++) {
    pages.push(<Page index={i} key={i} />);

    // 반복문 안에 hook(useSWR) 사용 불가
    // const { data } = useSWR(`/api/data?page=${i}`)
    // pages.push(data)
  }

  return (
    <div>
      {pages}
      <button onClick={() => setCnt(cnt + 1)}>Load More</button>
    </div>
  );
}
```

- useSWRInfinite
  - `getKey` : 인덱스와 이전 페이지 데이터를 받고 페이지의 키를 반환하는 함수(index, previousPageData) 인자를 받는다.
  - `size`: 가져*올* 페이지 및 반환*될* 페이지의 수
  - `setSize`: 가져와야 하는 페이지의 수를 설정

```jsx
// 생김새
import useSWRInfinite from 'swr/infinite'

const { data, error, isValidating, mutate, size, setSize } = useSWRInfinite(
  getKey, fetcher?, options?
)
```

```jsx
// 각 페이지의 SWR 키를 얻기 위한 함수,
// `fetcher`에 의해 허용된 값을 반환합니다.
// `null`이 반환된다면, 페이지의 요청은 시작되지 않습니다.
const getKey = (pageIndex, previousPageData) => {
  if (previousPageData && !previousPageData.length) return null; // 끝에 도달
  return `/users?page=${pageIndex}&limit=10`; // SWR 키
};

function App() {
  const { data, size, setSize } = useSWRInfinite(getKey, fetcher);
  if (!data) return 'loading';

  // 이제 모든 users의 수를 계산할 수 있습니다
  let totalUsers = 0;
  for (let i = 0; i < data.length; i++) {
    totalUsers += data[i].length;
  }

  return (
    <div>
      <p>{totalUsers} users listed</p>
      {data.map((users, index) => {
        // `data`는 각 페이지의 API 응답 배열입니다.
        return users.map(user => <div key={user.id}>{user.name}</div>);
      })}
      <button onClick={() => setSize(size + 1)}>Load More</button>
    </div>
  );
}
```

### Data 가져오기 방식

- CSR - pre render 필요 없을 경우. useSWR 사용하면 됨
- SSG, SSR - 반드시 프리렌더 필요

### 출처

[데이터 가져오기를 위한 React Hooks - SWR](https://swr.vercel.app/ko/docs/getting-started)
