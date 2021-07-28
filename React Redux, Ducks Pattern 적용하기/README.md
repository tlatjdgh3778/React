## 기존 Redux
`Ducks pattern` 을 적용하기 전에 redux의 폴더 구조를 `action`, `reducer`, `actionTypes`(Action type은 Action과 Reducer에서 둘 다 쓰이기 때문에 따로 분리해서 관리하기도 함) 폴더로 나누어서 관리를 했다.

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/68accf19-f922-4351-b603-1beee5118632/image.png" /></p>

```js
// store/index.js
import { createStore } from "redux";
import rootReducer from "./rootReducer";

const store = createStore(rootReducer);

export default store;
```

```js
// store/types/counter.js
// action types
// action creator와 reducer에 쓰임
export const INCREMENT = "INCREMENT";
export const DECREMENT = "DECREMENT";
```

```js
// store/actions/counter.js
import { INCREMENT, DECREMENT } from "../types/counter";

// action creator
export const increment = () => {
  return {
    type: INCREMENT
  };
};

export const decrement = () => {
  return {
    type: DECREMENT
  };
};
```

```js
// store/reducer/counter
import { DECREMENT, INCREMENT } from "../types/counter";

// reducer
const initialState = {
  count: 0
};

const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        count: state.count + 1
      };
    case DECREMENT:
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
};

export default counterReducer;
```

이런식으로 `Counter`에 대한 파일들이 나누어져 있기 때문에 관련된 기능을 수정하려고 하면 모든 파일들을 수정해야 하는 불편함이 있다.

이런 불편함들을 해결하고자 나온 것이 **Ducks Pattern** 이다.

## Ducks Pattern
기존 폴더 구조가 구조 중심으로 나누어서 관리를 했다면 **Ducks Pattern** 에서는 폴더를 기능 중심(모듈)으로 나누어서 관리를 한다.

`{actionTypes, actions, reducer}` 를 한 파일로 모아서 관리를 하는데 이 파일 하나하나를 module 이라고 부른다.

**Ducks Pattern** 을 적용할 때 지켜야 하는 몇가지 규칙이 있다.

* MUST `export default` a function called `reducer()`
* MUST `export` its action creators as functions
* MUST have action types in the form `npm-module-or-app/reducer/ACTION_TYPE`
* MAY `export` its action types as `UPPER_SNAKE_CASE`, if an external `reducer` needs to listen for them, or if it is a published reusable library

그럼 이제 이 규칙과 함게 위의 코드를 **Ducks Pattern** 으로 변경해보자.

우선 `store` 폴더 안에 `module` 폴더를 만든 후 `counter.js` 파일을 생성하고 코드를 작성한다.


**1. MUST `export default` a function called `reducer()`**
해당 규칙은 reducer 함수를 `reducer` 라는 이름으로 `export default` 해주어야 하는 조건이다.

```js
// ...
// reducer
const initialState = {
  count: 0
};

export default function reducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        count: state.count + 1
      };
    case DECREMENT:
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
};
```

**2. MUST `export` its action creators as functions**
해당 규칙은 `action creator` 함수를 `export` 해주어야 하는 조건이다.   
```js
// ...
// action creators
export const increment = () => {
  return {
    type: INCREMENT
  };
};

export const decrement = () => {
  return {
    type: DECREMENT
  };
};
```

**3. MUST have action types in the form `npm-module-or-app/reducer/ACTION_TYPE`**
actionTypes 의 네이밍 규칙을 정하고 있다.

이렇게 하는 이유는 다른 모듈과 액션 이름이 중복되는 것을 방지하기 위해서이다.

```js
// actionTypes
// my-app/counter/INCREMENT 에서 my-app 은 생략해도 딱히 상관없을듯,,?

export const INCREMENT = "counter/INCREMENT";
export const DECREMENT = "counter/DECREMENT";
```

**4. MAY `export` its action types as `UPPER_SNAKE_CASE`, if an external `reducer` needs to listen for them, or if it is a published reusable library**
actionTypes 를 `UPPER_SNAKE_CASE`로 하라는 뜻이다.

```js
// actionTypes
// UPPER_SNAKE_CASE 사용
export const INCREMENT = "counter/INCREMENT";
export const DECREMENT = "counter/DECREMENT";
```

`counter.js` 전체 코드

```js
// actionTypes
export const INCREMENT = "counter/INCREMENT";
export const DECREMENT = "counter/DECREMENT";

// actions
export const increment = () => {
  return {
    type: INCREMENT
  };
};

export const decrement = () => {
  return {
    type: DECREMENT
  };
};

// reducer
const initialState = {
  count: 0
};

export default function reducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT:
      return {
        ...state,
        count: state.count + 1
      };
    case DECREMENT:
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
};
```

-------

module 폴더에 각각의 파일들이 `reducer` 함수를 `export default` 로 내보내고 있기 때문에 `index` 파일을 만들어 각각 리듀서들을 합칠 수 있다.

```js
// module/index.js
import { combineReducers } from "redux";
import counter from "./counter";
// ex) import input from "./input";

export default combineReducers({
  counter
  // input
});
```

합친 reducer 들을 `store` 가 저장된 파일에서 불러오면 된다.

```js
import { createStore } from "redux";
import rootReducer from "./module/index";

const store = createStore(rootReducer);

export default store;
```

<p align="center"><img src="https://images.velog.io/images/tlatjdgh3778/post/56e11803-ba9c-4142-9ca0-1d85d8d4bf1c/image.png" /></p>

## 참고
* [Ducks Pattern](https://github.com/erikras/ducks-modular-redux)
* [코드 전체보기](https://codesandbox.io/s/react-redux-ducks-pattern-5sp9y)
