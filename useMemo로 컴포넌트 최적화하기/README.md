# useMemo

`useMemo` 는 [useCallback](https://velog.io/@tlatjdgh3778/React-Hooks-React.memo%EC%99%80-useCallback%EC%9C%BC%EB%A1%9C-%EC%B5%9C%EC%A0%81%ED%99%94%ED%95%98%EA%B8%B0)과 유사한 방식으로 최적화에 사용된다.
`useCallback` 은 메모이제이션 된 함수를 반환하는 반면에 `useMemo` 는 메모이제이션 된 **값**을 반환한다.

다음의 예제를 보자.

```js
// App.js
...
  return (
    <div className="App">
      <Counter />
    </div>
  );
...
```

```js
// components/Counter.jsx
import React, { useState } from "react";

const Counter = () => {
  const [counterOne, setCounterOne] = useState(0);
  const [counterTwo, setCounterTwo] = useState(0);

  const incrementOne = () => {
    setCounterOne(counterOne + 1);
  };

  const incrementTwo = () => {
    setCounterTwo(counterTwo + 1);
  };

  const isEven = () => {
    return counterOne % 2 === 0;
  }

  return (
    <div>
      <div>
        <button onClick={incrementOne}>Counter One - {counterOne}</button>
        <span>{isEven() ? " 짝수" : " 홀수"}</span>
      </div>
      <div>
        <button onClick={incrementTwo}>Counter Two - {counterTwo}</button>
      </div>
    </div>
  );
};

export default Counter;
```

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/2a38d9c1-5312-4a9d-8429-c97ab70e7efb/useMemo(1).gif" />
</p>

`Counter One` 버튼을 누르면 `counterOne` 변수가 +1씩 증가하고 홀수, 짝수를 나타내준다. `Counter Two` 버튼을 누르면 `counterTwo` 변수가 +1씩 증가하는 간단한 컴포넌트이다.

이제 코드를 다음과 같이 수정해보자.

```js
// components/Counter.jsx
...
const isEven = () => {
    let i = 0;
    while (i < 2000000000) i++;
    return counterOne % 2 === 0;
};
...
```

`isEven` 함수를 실행할 때마다 `i` 가 `0 ~ 2000000000` 까지 매번 루프를 돌 것이다.

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/5bb28057-7bdf-44ba-87e2-64b2625dafdf/useMemo(2).gif" />
</p>

실행 화면을 보면 `Counter One` 버튼을 누를 때 딜레이가 생기는데 `i` 가 `0 ~ 2000000000` 까지 루프를 돌기 때문이다.

그런데 왜 `Counter Two` 버튼에는 `isEven` 함수를 등록하지 않았는데도 딜레이가 생기는 것일까?

그건 `counterTwo` 의 상태가 변할 때마다 컴포넌트가 리렌더링되는데 그 때 
```js
<span>{isEven() ? " 짝수" : " 홀수"}</span>
```

해당 코드의 `isEven`이 매번 실행되기 때문이다.

`Counter Two` 버튼을 누를 때는 `isEven` 의 `return` 값을 매번 계산할 필요가 없고 이전에 `Counter One` 버튼을 눌렀을 때의 값을 사용하면 된다. 

## useMemo
연산된 값을 재사용하기 위해서는 `useMemo` 를 사용하면 된다. 사용 방법은 `useCallback` 이랑 매우 비슷하다.

```js
// components/Counter.jsx
import React, { useState, useMemo } from 'react';
...
const isEven = useMemo(() => {
    let i = 0;
    while (i < 2000000000) i++;
    return counterOne % 2 === 0;
}, [counterOne]);
...

```

`useMemo` 의 첫 번째 파라미너에 함수를 넣어주고 두 번째 파라미터에는 의존성 배열을 넣어주는데 이 값이 변경될 때만 함수를 실행하고 변경되지 않으면 이전에 연산한 값을 재사용한다.

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/84b07246-13f7-467c-a5d8-eaa865feefc9/useMemo(3).gif" />
</p>

`Counter One` 버튼을 눌렀을 때에는 `isEven` 함수가 실행되기 때문에 딜레이가 있지만 `Counter Two` 버튼을 누르면 딜레이가 없어진 것을 볼 수 있다.

여기까지 `useMemo` 를 사용해서 연산한 값을 재사용하는 방법을 알아보았다.

## 참고
* [Codevolution - useMemo](https://www.youtube.com/watch?v=qySZIzZvZOY)
* [코드 전체보기](https://codesandbox.io/s/usememo-hook-hcpu7?file=/src/components/Counter.jsx)
