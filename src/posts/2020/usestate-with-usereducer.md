---
title: useState 그리고 useReducer 
description: 적시 적소에 이들을 써보자
date: 2020-06-24
tags:
  - Programming
layout: layouts/post.njk
------

맨 처음 [hooks](https://reactjs.org/docs/hooks-intro.html)를 공부하면서 접하게 된 함수는 `useState`다. `useState`를 처음 사용하면서 든 생각은 너무나 편리하다는 것이었고, 이것으로 모든 문제가 해결될 거라 생각했다. 하지만 앱의 규모가 커지면서 여러 파일에서 공유해야 할 global state의 필요성이 생겼고, 이를 해결하기 위해 `useReducer`을 사용하게 되었다. 이 글에서는 `useState`는 무엇인지, `useReducer`은 무엇인지 알아보고 언제 무엇을 어떻게 쓸 것인지에 대해 알아보고자 한다.

## useState
먼저 리액트 공식문서에서 말하고 있는 [useState](https://reactjs.org/docs/hooks-reference.html#usestate)에 대해 살펴보자.

```js
const [state, setState] = useState(initialState);
```

- 상태 유지 값과 그 값을 갱신하는 함수를 반환한다
- 최초로 렌더링을 하는 동안, 반환된 state는 첫 번째 전달된 인자인 `initialState`의 값과 같다
- `setState`함수는 state를 갱신할 때 사용한다

아래의 카운터 예제를 보자.
```js
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  );
}
```

`initialCount`값을 받아오고 이 값은 `count`의 최초 값과 같다.
`-`버튼을 클릭하면 최신 `count`값에서 -1을 한 값으로 state가 업데이트된다.
`+`버튼을 클릭하면 최신 `count`값에서 +1을 한 값으로 state가 업데이트된다.

사실 `useState`는 hooks에서 가장 처음 소개되는 함수인 만큼 자주 사용되고, 그만큼 사용하기 쉽다. 하지만 이러한 `useState`를 사용할 때에도 주의할 점이 있다. 바로 `setState`는 비동기로 작동한다는 점이다.

리액트 문서를 살펴보면 이런 말이 있다.

> `this.props`와 `this.state`가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안 됩니다.

그러니 state값을 확인하고 싶을 때는 `useEffect`를 사용해 의존성 배열에 state 값을 넣은 후 확인하도록 하자!

## useReducer
리액트 공식문서에서는 [useReducer](https://ko.reactjs.org/docs/hooks-reference.html#usereducer)에 대해서 어떻게 말하고 있는지 알아보자.

- `useState`의 대체 함수이다
- `(state, action) => newState`의 형태로 reducer를 받고
dispatch 메서드와 짝의 형태로 state를 반환한다

`useState`에서 살펴본 카운터 예제를 `useReducer`를 사용해 변경한 아래의 코드를 살펴보자.
```js
import React, { useReducer } from 'react';
 
const counterReducer = (state, action) => {
  switch (action.type) {
    case 'INCREASE':
      return { ...state, count: state.count + 1 };
    case 'DECREASE':
      return { ...state, count: state.count - 1 };
    default:
      throw new Error();
  }
};
 
const Counter = () => {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });
 
  const handleIncrease = () => {
    dispatch({ type: 'INCREASE' });
  };
 
  const handleDecrease = () => {
    dispatch({ type: 'DECREASE' });
  };
 
  return (
    <div>
      <h1>Counter with useReducer</h1>
      <p>Count: {state.count}</p>
 
      <div>
        <button type="button" onClick={handleIncrease}>
          +
        </button>
        <button type="button" onClick={handleDecrease}>
          -
        </button>
      </div>
    </div>
  );
};
 
export default Counter;
```

우선 `counterReducer`이라는 이름의 리듀서를 만들어준다. `state`와 `action` 인자로 가지고 있고, 액션 타입의 케이스에 따라 state를 업데이트해 준다.

> `...state`는 [ES6의 Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 이다. 매우 자주 쓰게 될 것이므로 알아두자.

컴포넌트 안에서 `useReducer`을 사용해 위에 정의해둔 `counterReducer` 리듀서와 초기 값을 받아온다. `dispatch`의 첫 번째 인자는 액션의 `type`이고, 두 번째 인자는 `payload`다. 액션의 타입을 체크한 후 state object를 업데이트하고, 업데이트된 state를 리턴하고 있다.

## useState vs. useReducer
`useState`와 `useReducer`에 대해 어느 정도 알아봤다. 그러면 이러한 궁금증이 들 수 있다. 둘 다 `state`를 업데이트하는 건데, 언제 `useState`를 쓰고 언제 `useReducer`을 쓰면 되는 거지? 내가 일을 하면서 깨달은 한 가지 사실은, 이런 질문을 미리 걱정하지 말고 이런 질문이 자연스럽게 들 때가 오기를 기다리라는 것이다! 생각보다 그 순간은 빨리 찾아올 것이니..

[리액트 문서](https://ko.reactjs.org/docs/hooks-reference.html#usereducer)에서는 아래와 같이 말하고 있다.

> 다수의 하윗값을 포함하는 복잡한 정적 로직을 만드는 경우나 다음 state가 이전 state에 의존적인 경우에 보통 `useState`보다 `useReducer`를 선호합니다.

이번에는 [useReducer vs useState in React - RWieruch](https://www.robinwieruch.de/react-usereducer-vs-usestate#when-to-use-usestate-or-usereducer) 블로그에서 말하고 있는 `useState`와 `useReducer`의 사용에 대해 살펴보자.

### 이럴때 useState를 사용해라!
- `state`가 원시타입인 경우
- 간단한 `state` 업데이트가 필요한 경우
- 컴포넌트 안에 비즈니스 로직이 있는 경우 (나의 경우에는 뷰 안에 비즈니스 로직이 있는 것을 좋아하지 않는다)
- 규모가 작은 앱 (구분이 애매모호하긴 하지만)

### 이럴때 useReducer를 사용해라!
- `state`가 객체인 경우 
- 복잡한 `state` 업데이트가 필요한 경우
- 복잡한 비즈니스 로직은 `reducer`을 사용하는게 좋다
- 컴포넌트 트리 (여러개의 컴포넌트) 안에서 업데이트 되어야하는 `state`
- 유지 보수의 용이함을 위해

## 마무리
이번 글에서는 `useState`와 `useReducer`에 대해서 알아보았다. 나는 `useReducer`와 `Context`를 함께 사용해 스토어를 따로 만들어 사용하고 있는데 이 방법에 대해서도 나중에 다뤄보고 싶다. 나는 사실 객체지향 프로그래밍 경험은 없는 데다 함수형 프로그래밍 경험도 이번이 처음이다. 하지만 사용하면 할수록 함수형 프로그래밍의 편리함을 느끼고 있다. 가장 좋은 점은 잘 정리된 문서가 항상 내 뒤에 든든하게 있다는 것 아닐까!