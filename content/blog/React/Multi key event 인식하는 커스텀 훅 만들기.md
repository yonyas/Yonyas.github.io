---
title: 'Multi key event 인식하는 커스텀 훅 만들기'
date: 2022-09-04
category: 'React'
draft: true
---

`command + A`, `alt + S` 같은 조합키가 눌렸을 때 함수를 실행하려면 어떻게 해야 할까.

스택오버플로우에 보면 다음과 같이 나와 있다.

```ts
document.onkeydown = keydown;

function keydown(evt) {
  if (!evt) evt = event;
  if (evt.ctrlKey && evt.altKey && evt.keyCode === 115) {
    alert('CTRL+ALT+F4');
  } else if (evt.shiftKey && evt.keyCode === 9) {
    alert('Shift+TAB');
  }
}
```

참고로 맥에서는 `command` 가 `event.metaKey`  이다.
위 코드에서 alert 부분 대신 함수를 호출해주면 될 것 같다.

그럼 훅으로 만들어보자.

```ts
function useOnMultiKeyDown(
 combineKey: 'commandOrCtrl' | 'alt' | 'shift',
 keyCode: KeyboardEvent["key"],
 onKeyDown: VoidFunction,
): void {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      // 기본 동작인 텍스트 전체 클릭을 막아준다.
      e.preventDefault();

      if (e.key === key) {
        if (
          combineKey === "commandOrCtrl" &&
          (e.metaKey || e.ctrlKey)
        ) {
          onKeyDown();
        } else if (combineKey === "alt" && e.altKey) {
          onKeyDown();
        } else if (combineKey === "shift" && e.shiftKey) {
          onKeyDown();
        }
      }
    };

    document.addEventListener("keydown", handleKeyDown);
    return () => {
      documet!.removeEventListener(
        "keydown",
        handleKeyDown
      );
    };
  }
 }, [onKeyDown]);
}
```

1. 문제는 이렇게 하면 다른 모든 조합키 및 input에서 text 입력이 동작이 안 된다.

2. 해결하려면 원하는 element에서만 동작하도록 하려면 props로 element 를 받아야 한다. 그리고 preventDefault() 의 위치를 이동해야 한다.

아래는 수정한 코드이다.

```ts
function useOnMultiKeyDown(
 combineKey: 'commandOrCtrl' | 'alt' | 'shift',
 keyCode: KeyboardEvent["key"],
 onKeyDown: VoidFunction,
 element: HTMLElement,
): void {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === key) {
        if (
          combineKey === "commandOrCtrl" &&
          (e.metaKey || e.ctrlKey)
        ) {
          e.preventDefault();
          onKeyDown();
        } else if (combineKey === "alt" && e.altKey) {
          e.preventDefault();
          onKeyDown();
        } else if (combineKey === "shift" && e.shiftKey) {
          e.preventDefault();
          onKeyDown();
        }
      }
    };

    if (element !== null) {
      element.addEventListener("keydown", handleKeyDown);
      return () => {
        element!.removeEventListener("keydown", handleKeyDown);
      };
    }
  }
 }, [onKeyDown, element]);
}

```

### 참고

- [https://stackoverflow.com/questions/5203407/how-to-detect-if-multiple-keys-are-pressed-at-once-using-javascript](https://stackoverflow.com/questions/5203407/how-to-detect-if-multiple-keys-are-pressed-at-once-using-javascript)
