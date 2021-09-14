---
title: 'promise 를 써보자'
date: 2021-09-14
category: 'JavaScript'
draft: false
---

Promise는 인자가 두개(resolve, reject)인 콜백함수를 인자로 받는다.

```js
//Produce Promise
const getName = id => {
	return new Promise((resolve, reject) => {
		const user = axios.get(`api/${id}`);
		if (user) {
			resolve(user);
		} else {
			reject(`Network error`);
		}
	}
}

//Consume Promise
getName('23')
.then(name => console.log(name))
.catch(err => console.log(err))
```

### async await을 promise로 바꿔보자

```js
const getPhotoInfo = async id => {
  const res = await axios.get(`api/${id}`);
  return res;
};

//promise 로 바꿔보자
const getPhotoInfo = id => {
  return new Promise((resolve, reject) => {
    const res = axios.get(`api/${id}`);
    if (res) {
      resolve(res);
    } else {
      reject(`Network error`);
    }
  });
};

//Promise consume 해보자
getPhotoInfo(id)
  .then(res => {
    if (res.status === 200) {
      setPhoto(res.data);
    } else {
      alert('다시 시도해 주십시오');
    }
  })
  .catch(error => console.log(error));
```
