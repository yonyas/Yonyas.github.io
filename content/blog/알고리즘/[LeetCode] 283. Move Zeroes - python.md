---
title: '[LeetCode] 283. Move Zeroes - python'
date: 2021-03-28
category: '알고리즘'
draft: false
---

![](https://images.velog.io/images/yonyas/post/b78afaaf-519d-4a09-bf06-24db73328119/%EB%A6%AC%ED%8A%B8%EC%BD%94%EB%93%9C.png)

코딩테스트를 위해 리트코드 사이트에서 easy문제부터 풀어보고 있다. 얼마가 걸리든 일단 혼자 풀어보고 Discuss에서 천재들의 답을 보며 자괴감을 느끼고 있다. 😅

## 283번 문제

리스트에서 0인 값들을 뒤로 보내고 0이 아닌 숫자들의 순서는 보존해야 한다.
Note that you must do this in-place without making a copy of the array.

```
Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

## 풀이

### 내 풀이

_처음에는 0인 숫자를 찾아서 리스트에서 제거한 후, 리스트 뒤쪽에 append로 넣는 방식을 했는데 그렇게 하면 [0, 0, 1] 일 때 [0, 1, 0]으로 바뀌어서 틀린 답이 나온다._

앞에서부터 훑으면서 0이 아닌 숫자가 있다면 해당 리스트에서 pop으로 뺀 뒤, 리스트 앞쪽으로 차곡차곡 쌓게끔 풀었다.

```python
cnt = 0
for i, x in enumerate(nums):
  if x != 0:
    nums.pop(i)
    nums.insert(cnt, x)
    cnt += 1
```

속도는 제출자의 24%보다 빠르다.

### 고수의 풀이 1

```python
zero = 0  # records the position of "0"
for i in range(len(nums)):
  if nums[i] != 0:
    nums[i], nums[zero] = nums[zero], nums[i]
    zero += 1
```

투 포인터로 훑는다. 하나는 순서대로, 하나는 0이 아닌 숫자들을 찾는다. 그리고 서로 교환한다.

**풀이 예시**
포인터를 하나는 ^(zero), 하나는 @(for문의 i)으로 표현하겠다.

```python
[0, 1, 0, 3, 12]
 ^  @

# 스왑 후 ^는 하나 뒤로, @ 는 0이 아닌 숫자를 찾아 간다.
[1, 0, 0, 3, 12]
    ^     @

# 스왑
[1, 3, 0, 0, 12]
       ^      @

#스왑 & 완성
[1, 3, 12, 0, 0]
```

### 고수의 풀이 2

```python
count=nums.count(0)
nums[:]=[i for i in nums if i != 0]
nums+=[0]*count
```

nums 리스트에 있는 0의 개수를 센다. nums에 0이 아닌 숫자만 남겨둔다. 0의 개수만큼 nums 뒤에 붙인다.

이 풀이방법은 리스트를 따로 만들지 말라는 말을 어겼기 때문에 베스트는 아니지만
3줄만으로 코드를 짰고 방식도 괜찮아 보여서 올렸다.
