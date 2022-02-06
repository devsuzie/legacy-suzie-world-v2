---
title: Context API vs. Redux
description: Redux에 대해서, 그리고 Redux와 Context API의 차이에 대해서
date: 2020-09-08
tags:
  - Programming
layout: layouts/post.njk
------

리액트로 작업을 하면서 `Context API`와 `useReducer` Hook을 사용해 글로벌 상태를 관리하고 상태 관리 로직을 분리했다. 하지만 이러한 개발 방식은 리덕스에서 먼저 나온 것이다. 리덕스를 사용하면 글로벌 상태를 관리할 수 있다는 점에서 비슷한데, 그렇다면 이 둘의 차이는 무엇일까? 먼저, 리덕스 공식 문서에서 설명하는 리덕스에 대해 알아보자.

## 리덕스
> 참고 링크: [Redux Overview and Concepts](https://redux.js.org/tutorials/essentials/part-1-overview-concepts)

### 리덕스는 무엇일까
리덕스는 `action`이라는 일종의 이벤트를 사용해서 앱을 업데이트 및 관리해주는 라이브러리이다. 어플리케이션 어디서든 `state`를  사용할 수 있고, 이를 업데이트 해주기 위해서 `store`를 사용한다.

### 리덕스는 왜, 언제 쓰는가
리덕스는 `global state`를 관리하도록 도와준다. 리덕스에서 제공하는 툴과 패턴을 사용하면 왜, 언제, 어디서, 어떻게 state가 업데이트 되었고 어떻게 그런 업데이트가 일어나게 되었는지 알기 쉽다. 아래의 경우에 리덕스를 사용한다.

- 앱 안에서 사용해야 할 `state`의 수가 많을 경우
- 앱의 `state`가 업데이트 되어야 할 경우
- `state`를 업데이트 하는 로직이 복잡할 경우
- 앱이 중간 규모 또는 큰 규모일 경우

### Immutability
내가 지난번 작성한 [Immutable Data](https://www.suzie.world/blog/immutable-data) 블로그 글에 나오는 그 `Immutability`가 맞다. 리듀서는 순수한 함수여야 한다. 리듀서는 이전 상태를 절대 건들이지 않고, 변화를 일으킨 새로운 상태 객체를 만들어서 반환한다. 또한, 똑같은 파라미터로 호출된 리듀서 함수는 언제나 똑같은 결과값을 반환해야 한다.

```js
const obj2 = {
  // copy obj
  ...obj,
  // overwrite a
  a: {
    // copy obj.a
    ...obj.a,
    // overwrite c
    c: 42
  }
}
```

> 리덕스는 모든 상태가 `immutable`하게 업데이트 되는 것에 기반한다.

### 리덕스에서 쓰이는 용어
#### action
`action`은 `type`필드를 가진 자바스크립트 객체이다. 액션을 앱에서 일어나는 이벤트라고 생각할 수 있다. action 객체는 액션을 설명해주는 이름을 가진 `type` 필드와 어떤 일이 일어나는지 설명하는 곳인 `payload` 필드로 이루어져있다. 

```js
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

> `type`필드는 필수적이지만 그 외의 값들은 마음대로 넣을 수 있다.

#### action creators
액션 생성 함수는 `action`을 만들고 반환하는 함수다. 이 액션 생성 함수를 사용하면 컴포넌트에서 액션을 발생시키기 쉬워진다. `export` 키워드를 붙여 다른 파일에서 불러와 사용할 수 있다.

```js
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```

#### reducer
리듀서는 최신 `state`와 `action` 객체를 받고, 어떻게 상태를 업데이트 할지 결정한 후 새로운 상태값을 반환한다.
```
(state, action) => newState
```

리듀서는 어떠한 비동기 로직도 하지 않고, 기존의 상태를 수정할 수 없다. 대신 기존 상태값의 복사본을 만들어 `immutable`한 업데이트를 한다. 먼저, 리듀서가 전달받은 액션을 다루는지 확인하고 만약 그렇다면 상태값을 복사한 후 업데이트하고 반환한다. 전달받은 액션을 다루지 않는 경우에는 존재하는 상태값을 반환한다. 

```js
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // Check to see if the reducer cares about this action
  if (action.type === 'counter/increment') {
    // If so, make a copy of `state`
    return {
      ...state,
      // and update the copy with the new value
      value: state.value + 1
    }
  }
  // otherwise return the existing state unchanged
  return state
}
```

#### store
리덕스 앱의 상태와 리듀서는 `store`안에 존재한다. 현재 상태를 반환하는 `getState` 메서드를 가지고 있다. 

```js
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

#### dispatch
리덕스 스토어는 `dispatch` 메서드를 가지고 있다. 상태를 업데이트 할 수 있는 유일한 방법은 `store.dispatch()`를 호출하고 액션 객체를 넘겨주는 것이다. 스토어는 리듀서 함수를 실행할 것이고 새로운 상태 값을 저장한다. 그러면 우리는 `getState()`를 통해 업데이트 된 값을 받아올 수 있다.

```js
store.dispatch({ type: 'counter/increment' })

console.log(store.getState())
// {value: 1}
```

> 액션을 디스패칭 해오는 것을 “이벤트를 불러일으키는 것”이라고 생각할 수 있다.

#### selectors
`selectors`는 스토어의 상태 값에서 특정 정보를 추출할 수 있는 함수이다. 앱이 커지면, 앱의 여러 곳에서 공통된 데이터를 읽어야 하기 때문에 selectors를 사용해 중복되는 로직을 피할 수있다. 

### 리덕스가 동작하는 방식
리덕스가 무엇인지, 기본 용어에 대해서 알아봤다. 아래에 리덕스가 동작하는 방식을 간단하게 정리해보았다.

```
Action Creator -> Action (type + payload) -> dispatch -> Reducers -> State
```

## Context API와 리덕스의 차이
### 미들웨어
리덕스에는 미들웨어라는 개념이 존재한다. 미들웨어를 사용하면 액션 객체가 리듀서에서 처리되기 전에 원하는 작업을 수행할 수 있다. 미들웨어는 비동기 작업을 할 때 많이 사용된다. 가령 API를 연동해야 할 때 미들웨어를 사용할 수 있다. 

리덕스 미들웨어는 일반적으로 라이브러리를 설치하여 사용한다. 물론 직접 만들어서 사용할 수도 있다. 리덕스 미들웨어 라이브러리로는 `redux-thunk`, `react-saga` 등이 있다. 이런 라이브러리를 사용하면 특정 액션을 모니터링할 수 있고,  특정 액션이 디스패치 됐을 때 원하는 함수를 호출할 수 있다.

### connect 함수와 hooks
리덕스에서 `connect`함수를 사용하면 상태, 액션 생성 함수를 컴포넌트의 props로 받아올 수 있다. 또한 `useSelector` hook을 사용하면 상태를 조회하고 액션을 디스패치 할 수 있다. 이들을 통해 실제 상태가 바뀔 때만 컴포넌트가 리랜더링 될 수 있게 한다. 

반면 Context API에서는  컨텍스트가가 지니고 있는 상태가 바뀌면 `context`의 `provider` 내부 컴포넌트가 모두 리렌더링 된다는 차이점이 있다. 

### Redux DevTools
[Redux DevTools Extension](https://github.com/zalmoxisus/redux-devtools-extension)은 시간 경과에 따른 Redux 저장소의 상태 변경을 확인할 수 있다. 이를 통해 "시간 여행 디버깅"과 같은 강력한 기술을 사용할 수 있다. 또한 앱을 효과적으로 디버깅하는 것이 가능하다. 

### 그래서 뭘 써야 하는걸까?
Dan Abramov의 [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)라는 글을 살펴보자.

규모가 큰 앱에서는 리덕스가 필요할 때가 올 것이라는 사실은 알고 있을 것이다. 그런데 그는 사람들이 그런 일에 대해서 너무 일찍 걱정한다고 말한다. “우리 앱이 리덕스가 없이 규모가 커지면 어떻게 되는 거지?”라는 걱정에 리덕스를 선택하고, 나중에는 간단한 기능 하나를 위해서 세 개의 파일을 작업하는 것에 얼굴을 찌푸리게 된다. 

리덕스를 사용하기 위해서는 아래와 같은 작업을 해야 한다.
- 앱 상태를 plane object와 array로 작성
- 시스템에서 일어난 변화를 plain object로 작성
- 순수 함수를 바꾸기 위한 로직 작성 

우리는 이러한 사항이 앱을 만들때 방해하지 않을 것인지 깊게 고민해봐야한다. 만약 리액트를 배우고 있거나 이제 시작했다면, 리덕스는 첫 번째 선택지가 아닐 거라고 단은 말한다. 리액트를 사용해보고, 리덕스의 필요성이 느껴지면 그때 리덕스에 대해 공부하고 사용해도 늦지 않을 것이다. 

## 마무리
`Context API`와 `useReducer`을 이용해 앱을 개발하면서 이런 개발 방법의 전신(?)이 되는 리덕스에 대한 궁금증이 생겼다. 이번 글을 작성하면서 리덕스가 어떤 것인지, `Context API`와 어떤 차이가 있는지에 대해 알게 되었다. 단이 말한 것처럼 리덕스가 필요해지는 시기는 규모가 큰 앱이라면 분명히 올 것이고, 그때가 오면 리덕스를 선택하면 될 것이라고 생각한다. 그 때 리덕스를 잘 사용하기 위해서는 미리 리덕스와 미들웨어의 동작법에 대해 공부해야 할 것이다. 다음에는 미들웨어를 직접 만들어보고 이에 대한 글을 작성해보고 싶다. 끗!