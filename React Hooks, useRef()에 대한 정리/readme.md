# React Hooks, useRef()에 대한 정리

`useRef`는 함수형 컴포넌트에서 DOM node에 직접적인 접근을 가능하게 해주는 Hook이다.

클래스형 컴포넌트에서는 `createRef`를 사용한다.

## DOM 선택하기
예를 들어서, 페이지가 처음 로드됐을 때 `input`에 포커스를 줄 수 있다. 

아래 코드는 `input` 태그만 화면에 띄우는 코드이다.

```js
// components/FormInput.jsx
import React from "react";

const FormInput = () => {
  return (
    <>
      <h2>Input</h2>
      <input type="text" /> 
    </>
  );
};

export default FormInput;

```
<p aling="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/be474b1a-d768-49dc-bd3d-addf09bcd870/normal.gif" /></p>

`useRef`를 사용하면 페이지가 로드됐을 때 `input` 태그에 `focus`를 줄 수 있다.

```js
// components/FormInput.jsx
import React, { useEffect, useRef } from "react";

const FormInput = () => {
  const inputRef = useRef(null); // ref객체 생성

  useEffect(() => {
    // focus the input
    // current 프로퍼티를 사용해서 DOM에 직접적인 접근을 가능하게 해준다.
    inputRef.current.focus();
  }, []);
  return (
    <>
      <h2>useRef</h2>
      {/* 선택하고 싶은 DOM에 속성으로 ref값을 지정해준다.  */}
      <input ref={inputRef} type="text" /> 
    </>
  );
};

export default FormInput;

```

<p aling="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/8ac0664c-9cf8-47cd-afbb-f2a44a6c9f89/useRef.gif" /></p>

`input` 태그에 `focus`가 된 것을 확인할 수 있다.

## setInterval, setTimeout clear
`setInterval`, `setTimeout` 과 같은 함수는 clear를 해주지 않으면 메모리를 많이 소모하기 때문에 특정 상황이나 컴포넌트가 unmount 될 때는 clear를 꼭 해주어야 한다.

```js
import React, { useState, useEffect, useRef } from "react";

const FormInput = () => {
  const [timer, setTimer] = useState(0);
  const intervalRef = useRef();

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setTimer((prev) => prev + 1);
    }, 1000);

    return () => {
      clearInterval(intervalRef.current);
    };
  }, []);

  return (
    <>
      <h2>useRef</h2>
      Hook Timer - {timer}
      <button onClick={() => clearInterval(intervalRef.current)}>
        Clear interval
      </button>
    </>
  );
};

export default FormInput;
```

<p align="cener"><img src="https://images.velog.io/images/tlatjdgh3778/post/a1a78618-1213-4cc7-b1dc-1cfcc479dd72/timer.gif" /></p>

버튼을 클릭하면 `onClick` 함수가 실행되면서 `setInterval`을 clear 할 수 있다.

## 변수로 사용하기

## 참고
* [https://xiubindev.tistory.com/98](https://xiubindev.tistory.com/98)
