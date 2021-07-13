# React Hooks, useState() 대한 정리


## 1. 클래스 기반 상태 관리
React Hooks가 나오기전에는 컴포넌트의 상태 관리를 하기 위해서는 클래스 기반으로 컴포넌트를 작성해야 했다. 아래의 코드는 간단한 name, surname 입력을 할 수 있는 컴포넌트이다.
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

클래스 컴포넌트의 `this.state` 에 `name`, `surname` 을 저장해두고 input에 사용자가 입력 할 때마다 값이 갱신이 되면서 화면에 반영된다.

하지만 클래스 기반의 컴포넌트는 함수를 항상 `bind` 해주어야 하고, 상태나 상태를 관리하는 함수를 사용할 때에는 `this`, `this.state` 를 써서 접근해야 하는 등 간단한 컴포넌트를 만드는 데에도 귀찮고 불편한 점이 많았다.

하지만 리액트 16.8 버전부터 Hooks가 도입되면서 함수형 컴포넌트에서도 상태 관리를 할 수 있게 됐다.

## 2. 함수 기반 상태 관리
React Hooks에서 제공하는 `useState()` 함수를 사용해서 함수형 컴포넌트에서도 상태 관리를 할 수 있다.

`useState()` 의 기본적인 모습은 아래와 같다.
```js
const [value, setValue] = useState(initialValue);
```
`value` 는 상태 값을 저장하는 변수이고, `setValue` 는 상태 값을 변경하는 함수이다.
`useState` 의 인자로 초기값을 넘겨주면서 사용한다.

`useState` 는 배열을 반환하고 배열 구조 분해 문법으로 작성되었는데, 내부적으로는 다음과 같이 동작한다.

```js
const [fruit, setFruit] = useState('banana');

const fruitStateVariable = useState('banana'); // 두 개의 아이템이 있는 쌍을 반환
const fruit = fruitStateVariable[0]; // 첫 번째 아이템
const setFruit = fruitStateVariable[1]; // 두 번째 아이템
```

이제 위에서 클래스 기반으로 작성했던 컴포넌트를 함수 기반으로 바꾸어보자.

```js
import React, { useState } from "react";

const Hello = () => {
  const [name, setName] = useState("Mary");
  const [surname, setSurname] = useState("Poppins");
  
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

export default Hello;
```

state를 가져올 때에는 `name`, `surname`에 바로 접근할 수 있다.
state를 갱신할 때에도 `this`를 사용하지 않고 바로 호출이 가능하다.

## 참고
[YOUTUBE - React Today and Tomorrow and 90% Cleaner React With Hooks](https://www.youtube.com/watch?v=dpw9EHDh2bM)
