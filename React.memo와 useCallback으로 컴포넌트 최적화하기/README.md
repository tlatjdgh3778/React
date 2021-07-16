# React.memo와 useCallback

리액트는 컴포넌트를 렌더링 한 뒤 이전 렌더링된 결과와 비교하여 DOM 업데이트를 결정한다. 만약 렌더링 결과가 이전과 다르다면 DOM을 업데이트한다.

`React.memo()` 로 컴포넌트를 감싸면 리액트는 컴포넌트를 렌더링하고 그 결과를 메모이제이션한다. 다음 렌더링 시 리액트는 이전 `props`가 현재의 것과 같다면 리액트는 메모이제이션한 결과를 재사용해서 성능을 향상시킨다.

다음의 예제를 보자

[codesandbox에서 보기](https://codesandbox.io/s/usecallback-hooks-ivnzd?file=/src/components/ParentCoponent.jsx)

```js
// ParentComponent.js
import React, { useState } from "react";
import Title from "./Title";
import Count from "./Count";
import Button from "./Button";

const ParentComponent = () => {
  const [age, setAge] = useState(25);
  const [salary, setSalary] = useState(50000);

  const incrementAge = () => {
    setAge(age + 1);
  };

  const incrementSalary = () => {
    setSalary(salary + 1000);
  };

  return (
    <div>
      <Title />
      <Count text="Age" count={age} />
      <Button handleClick={incrementAge}>IncrementAge</Button>
      <Count text="Salary" count={salary} />
      <Button handleClick={incrementSalary}>IncrementSalary</Button>
    </div>
  );
};

export default ParentComponent;
```

```js
// Button.js
import React from "react";

const Button = ({ handleClick, children }) => {
  console.log("Rendering button - ", children);

  return <button onClick={handleClick}>{children}</button>;
};

export default Button;
```

```js
// Counter.js
import React from "react";

const Count = ({ text, count }) => {
  console.log(`Rendering ${text}`);

  return (
    <div>
      {text} - {count}
    </div>
  );
};

export default Count;
```

```js
// Title.js
import React from "react";

const Title = () => {
  console.log("Rendering Title");

  return <h2>useCallback Hook</h2>;
};

export default Title;
```

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/cc22211e-fcb0-4ea0-b6b7-47f8b3d6aa15/image.png" />
</p>

`Age`와 `Salary` 그리고 각각의 버튼을 누르면 `Age` 는 1씩 `Salary` 는 1000씩 증가하는 코드이다. 

처음 마운트 시
```js
<Title />
<Count text="Age" count={age} />
<Button handleClick={incrementAge}>IncrementAge</Button>
<Count text="Salary" count={salary} />
<Button handleClick={incrementSalary}>IncrementSalary</Button>
```
해당 코드 부분이 렌더링이 되면서 콘솔에 로그가 찍힌다.
  
<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/d07ecd55-d00b-4393-a1d8-458be2860fa4/image.png" />
</p>

문제는 다음인데, `IncrementAge` 버튼을 눌러보자.

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/f7398737-f6c6-4289-aced-27acbf16862b/image.png" />
</p>

이처럼 `Age` 뿐만 아니라 다른 컴포넌트들도 리렌더링 되는 것을 볼 수가 있다. 그 이유는 `setAge`가 실행되면서 `ParentComponent`와 하위 컴포넌트들도 전부 리렌더링 되기 때문이다. 만약 하위 컴포넌트들이 100개 200개가 있다고 한다면 성능에 좋지 않은 영향을 미치게 될 것이다.

## React.memo

이러한 성능 저하를 방지하기 위해서 `React.memo()` 를 사용할 수 있다.

```js
export default React.memo(Title);
export default React.memo(Count);
export default React.memo(Button);
// 내보내기 시 React.memo()로 감싸주기
```

`React.memo()` 를 사용하고 `IncrementAge` 버튼을 눌러보면 결과가 어떻게 나오는지 보자.

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/3b29f01b-61d1-4b93-bb6d-13b235e738d8/image.png" />
</p>

일단은 원하는대로 `Rendering Title`, `Rendering Salary` 부분은 로그에 찍히지 않았다. 하지만 `Rendering button - IncrementSalary` 는 로그에 찍혔는데 그 이유는 `ParentComponent` 가 렌더링이 될 때마다 `incrementAge` 함수와 `incrementSalary` 함수가 재생성되기 때문이다.

[함수 생성 비교](https://ui.toast.com/weekly-pick/ko_20190731#4-reactmemo-%EC%99%80-%EC%BD%9C%EB%B0%B1-%ED%95%A8%EC%88%98)

해당 부분을 보면 함수의 내용이 같아도 자기 자신만 동일하기 때문에 새로 생성된 함수는 이전의 함수와 비교하면 다르다고 나온다. 따라서 `React.memo`는 다른 `props` 로 보고 렌더링을 하는 것이다.

그러면 함수를 매번 재생성 하는 것을 방지할 수는 없을까?

## useCallback
`useCallback()` Hook은 함수를 새로 만들지 않고 메모이제이션 된 콜백을 반환한다. 즉 함수를 재사용 한다는 말이다.

사용 방법은 함수를 `useCallback()`으로 감싸주고 두 번째 인자로 의존성 배열을 넣어준다.
```js
// ParentComponent.js
// ...
const incrementAge = useCallback(() => {
  setAge(age + 1);
}, [age]); // age가 변경될 때만 incrementAge 생성

const incrementSalary = useCallback(() => {
  setSalary(salary + 1000);
}, [salary]); // salary가 변경될 때만 incrementSalary 생성

// ...
```

의존성 배열 안에 있는 값이 변경될 때만 해당 함수가 재생성이 되기 때문에 `incrementAge` 버튼을 눌렀을 때에는 `incrementSalary` 함수는 재생성이 되지 않고 따라서 `Rendering button - IncrementSalary` 도 출력되지 않는다.

<p align="center">
  <img src="https://images.velog.io/images/tlatjdgh3778/post/c445989e-0286-4520-822b-624b814b8998/image.png" />
</p>

여기까지 `React.memo` 와 `useCallback` 을 사용하여 컴포넌트를 최적화하는 방법을 연습해보았다. 하지만 둘 다 남발하는 것은 오히려 성능에 좋지 않다고 하기 때문에 적절한 부분에만 사용하는 것이 좋을 거 같다.  
## 참고

* [React.memo() 현명하게 사용하기](https://ui.toast.com/weekly-pick/ko_20190731)
* [Codevolution - useCallback](https://www.youtube.com/watch?v=IL82CzlaCys)
* [코드 전체보기](https://codesandbox.io/s/usecallback-hooks-ivnzd?file=/src/components/ParentCoponent.jsx)
