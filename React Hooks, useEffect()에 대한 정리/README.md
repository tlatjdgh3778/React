# React Hooks, useEffect()에 대한 정리

`useEffect` Hook 을 사용하면 함수형 컴포넌트에서도 `side effect` 를 수행할 수 있다.

> **side effect 란?**
> React 컴포넌트가 렌더링 된 후 비동기적으로 처리해야 하는 작업들을 말한다.
> 예를 들어서 API를 호출하는 경우 데이터를 비동기적으로 가져와야하는데, 만약 그렇지 않다면 데이터를 가져오는 시간동안 렌더링이 지연이 될 수도 있기 때문이다.

## 클래스 컴포넌트 side effect
side effect를 수행하는 시점은 리액트가 DOM을 업데이트 하고 난 이후이다. 때문에 클래스 컴포넌트에서 side effect를 수행하려면 `componentDidMount`, `componentDidUpdate` 안에 두면 된다. 

아래의 코드가 클래스 방식에서 side effect를 수행하는 방법이다.

```js
import React from "react";

class Hello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "Mary",
      surname: "Poppins"
    };
    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
  }

  // side effect
  componentDidMount() {
    document.title = this.state.name + " " + this.state.surname;
  }

  componentDidUpdate() {
    document.title = this.state.name + " " + this.state.surname;
  }
  
  handleNameChange(e) {
    this.setState({
      name: e.target.value
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }

  render() {
    return (
      <section>
        <div>
          <label>Name : </label>
          <input value={this.state.name} onChange={this.handleNameChange} />
        </div>
        <div>
          <label>Surname : </label>
          <input
            value={this.state.surname}
            onChange={this.handleSurnameChange}
          />
        </div>
      </section>
    );
  }
}

export default Hello;
```

## 함수 컴포넌트 side effect
함수 컴포넌트에서는 `useEffect` Hook을 사용하면 side effect를 수행하는게 가능하다.

```js
import React, { useEffect, useState } from "react";

const Hello2 = () => {
  const [name, setName] = useState("Mary");
  const [surname, setSurname] = useState("Poppins");

  useEffect(() => {
    document.title = name + " " + surname;
  });

  const handleNameChange = (e) => {
    setName(e.target.value);
  };

  const handleSurnameChange = (e) => {
    setSurname(e.target.value);
  };

  return (
    <section>
      <div>
        <label>Name : </label>
        <input value={name} onChange={handleNameChange} />
      </div>
      <div>
        <label>Surname : </label>
        <input value={surname} onChange={handleSurnameChange} />
      </div>
    </section>
  );
};

export default Hello2;
```

`useEffect`의 첫번째 인자로 오는 함수(effect)를 React는 기억하는데 DOM을 업데이트 하고 난 후에 이 effect를 수행한다. 

기본적으로는 첫 렌더링과 그 이후의 모든 업데이트에 대해서 effect를 수행하게 된다. 이는 클래스 컴포넌트의 `componentDidMount`, `componentDidUpdate`를 합쳐놓은 것과 같다.

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
});
// 첫 렌더링과 모든 업데이트 시마다 렌더링 된다.
```

불필요한 렌더링을 줄이고 싶다면 `useEffet`의 두 번째 인자인 *의존성 배열* 을 넣어주면 된다.

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, []);
// 컴포넌트가 마운트 됐을때만 실행된다. (componentDidMount)
```

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]);
// // count가 바뀔 때만 effect를 재실행
```

## Clean Up
만약 컴포넌트가 마운트 될 때 이벤트 리스너를 통해 이벤트를 추가하였다면 컴포넌트가 언마운트 될 때 이벤트를 삭제 해주어야 한다.

이벤트 리스너를 삭제해주지 않으면 컴포넌트가 리렌더링 될 때마다 새로운 이벤트 리스너가 핸들러에 바인딩 될 것이다. 컴포넌트가 자주 리렌더링 될 경우에는 이로 인해서 메모리 누수가 발생할 수 있다.

```js
// Hello.js
import React from "react";

class Hello extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "Mary",
      surname: "Poppins",
      width: window.innerWidth
    };

    this.handleNameChange = this.handleNameChange.bind(this);
    this.handleSurnameChange = this.handleSurnameChange.bind(this);
    this.handleResize = this.handleResize.bind(this);
  }
  
  componentDidMount() {
    document.title = this.state.name + " " + this.state.surname;
    window.addEventListener("resize", this.handleResize);
  }

  componentDidUpdate() {
    document.title = this.state.name + " " + this.state.surname;
  }

  handleResize() {
    console.log("resize Event");
    this.setState({
      width: window.innerWidth
    });
  }

  handleNameChange(e) {
    this.setState({
      name: e.target.value
    });
  }

  handleSurnameChange(e) {
    this.setState({
      surname: e.target.value
    });
  }

  render() {
    return (
      <section>
        <div>
          <label>Name : </label>
          <input value={this.state.name} onChange={this.handleNameChange} />
        </div>
        <div>
          <label>Surname : </label>
          <input
            value={this.state.surname}
            onChange={this.handleSurnameChange}
          />
        </div>
        <div>
          <label>Width : </label>
          {this.state.width}
        </div>
      </section>
    );
  }
}

export default Hello;
```

```js
// Container
import React, { useState } from "react";
import Hello from "./Hello";

const Container = () => {
  const [display, setDisplay] = useState(true);

  return (
    <div>
      <button onClick={() => setDisplay(!display)}>Toggle display</button>
      {display && <Hello />}
    </div>
  );
};

export default Container;
```

`Container` 컴포넌트 안에 버튼을 두어서 클릭시 언마운트 할 수 있게 설정을 해주었다.

![](https://images.velog.io/images/tlatjdgh3778/post/5740d99e-bc2f-4ca0-89b5-1154d228af2f/image.png)

화면의 크기를 변경할 때마다 `resize Event` 가 출력된다.

`Toggle display` 버튼을 눌러서 언마운트 시킨 뒤에 화면의 크기를 조정해보면

![](https://images.velog.io/images/tlatjdgh3778/post/8b55a1b8-e8d0-42b2-a145-b3e789cb8ffe/image.png)

오류가 발생하는데 전체 내용은 다음과 같다.

index.js:27 Warning: Can't perform a React state update on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in the componentWillUnmount method.

언마운트 된 컴포넌트의 상태를 변경하려고 해서 오류가 발생하는데 언마운트 될 때 이벤트 리스너를 삭제해주면 해결된다.

따라서 위의 코드에
```js
  componentWillUnmount() { // clean-up
    window.removeEventListener("resize", this.handleResize);
  }
```
를 추가해주면 오류가 발생하지 않는다.

그럼 이제 클래스로 작성된 컴포넌트를 함수 컴포넌트로 변경해보자. 

```js
import React, { useEffect, useState } from "react";

const Hello2 = () => {
  const [name, setName] = useState("Mary");
  const [surname, setSurname] = useState("Poppins");
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    document.title = name + " " + surname;
  });

  useEffect(() => {
    const handleResize = () => {
      console.log("Resize Event");
      setWidth(window.innerWidth);
  	};
    window.addEventListener("resize", handleResize);
 
    // clean-up
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  });

  const handleNameChange = (e) => {
    setName(e.target.value);
  };

  const handleSurnameChange = (e) => {
    setSurname(e.target.value);
  };

  return (
    <section>
      <div>
        <label>Name : </label>
        <input value={name} onChange={handleNameChange} />
      </div>
      <div>
        <label>Surname : </label>
        <input value={surname} onChange={handleSurnameChange} />
      </div>
      <div>
        <label>Width : </label>
        {width}
      </div>
    </section>
  );
};

export default Hello2;
```

클래스 컴포넌트의 clean-up은 `componentWillUnmount` 를 사용해서 구현했고 함수 컴포넌트에서의 clean-up은 `useEffect` 의 `return` 문에 clean-up 함수를 적어주면 된다.


## 참고
[YOUTUBE - React Today and Tomorrow and 90% Cleaner React With Hooks](https://www.youtube.com/watch?v=dpw9EHDh2bM)
[React 공식문서 - useEffect 사용하기](https://ko.reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)
