---
title: useCallback과 useMemo를 이용한 성능 최적화
description: useCallback, useMemo를 쓴다고 무조건 좋은 것일까?
date: 2020-07-16
tags:
  - Programming
layout: layouts/post.njk
------

나는 리액트와 훅스로 작업을 하면서 대부분의 함수를 `useCallback`으로 감싸고 있다. 그 이유는 간단하다. `useCallback`을 사용하면 함수를 재사용할 수 있기 때문이다. 컴포넌트가 리렌더링 될 때마다 새로 만들어지는 것이 아니라 재사용하기 때문에 최적화할 수 있다. 그런데 이런 `useCallback`을 쓰는 게 마냥 좋은 게 아니라고? `useMemo`는 또 뭘까? 성능 최적화와 이 둘 hooks에 대해 알아보자.

## useCallback
먼저 리액트 문서에서 말하는 [useCallback](https://reactjs.org/docs/hooks-reference.html#usecallback)에 대해서 알아보자.

```js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

* 메모이제이션된(이전에 계산한 - 메모리에 저장된 값) `콜백`을 반환한다
* `인라인 콜백`과 그것의 `의존성 값의 배열`을 전달한다
* 메모이제이션된 버전은 콜백의 의존성이 변경되었을 때에만 변경된다

문서의 주의 부분에서는 아래와 같은 경고를 한다.

> 의존성 값의 배열이 콜백에 인자로 전달되지는 않습니다. 그렇지만 개념적으로는, 이 기법은 콜백 함수가 무엇일지를 표현하는 방법입니다. 콜백 안에서 참조되는 모든 값은 의존성 값의 배열에 나타나야 합니다. 

우리는 사람이기 때문에 배열 안에 들어가야 할 것들을 분명히 빼먹을 수 있다. 그런 경우를 위해서  [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 패키지의 일부로써
 [exhaustive-deps](https://github.com/facebook/react/issues/14920) 규칙을 사용하기를 권장한다.

## useMemo
리액트 문서에서는 [useMemo](https://reactjs.org/docs/hooks-reference.html#usememo)를 어떻게 말하고 있는지 살펴보자.

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

* 메모이제이션된 `값`을 반환한다
* `생성 함수`와 그것의 `의존성 값의 배열`을 전달한다
* useMemo로 전달된 함수는 렌더링 중에 실행된다
* 배열이 없는 경우 매 렌더링 때마다 새 값을 계산하게 된다

> `useCallback(fn, deps)`은 `useMemo(() => fn, deps)`와 같다

`useCallback`은 함수를 반환하는 반면, `useMemo`는 연산된 값을 반환하고 있다. `useMemo`에서 memo는 “memozied”를 의미한다는 점에, 전에 계산한 값을 재사용한다는 것을 알 수 있다.

## useMemo과 useCallback은 언제 써야할까
> 아래의 글은 [When to useMemo and useCallback](https://kentcdodds.com/blog/usememo-and-usecallback) 블로그 글을 참고한 내용이다.

### useCallback의 경우
* 성능 향상을 위해 `React.useCallback`을 써야 한다고 들어왔다
* 그런데 왜 `useCallback` 을 쓰는 게 무조건 좋은 일은 아니라고 하는 걸까?

> 힌트: 모든 라인의 실행되는 코드들은 비용과 함께한다.

아래의 두 코드를 보자.

```js
const dispense = candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy));
};
```

```js
const dispense = React.useCallback(candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy));
}, []);

// 풀어 보면
const dispense = candy => {
  setCandies(allCandies => allCandies.filter(c => c !== candy));
};

const dispenseCallback = React.useCallback(dispense, []);
```

* `useCallback`이 어떻게 구현되었는지에 따라, 함수 정의를 위해 더 많은 메모리 할당을 하게 된다
* 기존 버전에서는 메모리 공간을 비우고 새로 생성된다
* `usecallback` 버전은 그렇지 않기 때문에 메모리 관점에서 좋지 않다고 할 수 있다

### useMemo의 경우
`useMemo` 는 value 타입과 관련 없이 메모이제이션을 적용한다. 아래 코드는 initialCandies 배열을 initialize하고 메모이제이션 한다.

```js
const initialCandies = React.useMemo(
 () => [‘snickers’, ‘skittles’, ‘twix’, ‘milky way’],
 [],
)
```

* useMemo를 사용함으로써 매 랜더마다 배열을 initalize하는 문제는 해결했지만, 과연 좋은 걸까?
* 답은 역시나! 아니다
* 또다시 함수를 call하고, 메모리 할당을 하고 있기 때문이다

> 성능 최적화는 비용이 들지만 항상 그 비용을 상쇄할 만큼의 이익을 가져다주지는 않는다.

### 그렇다면 언제 써야하는 걸까?
* Referential equality (참조 동일성)
* Computationally expensive calculations (계산적으로 비용이 많이 드는 계산)

#### Referential equality (참조 동일성)
리액트에서 참조 동일성이 중요해지는 두 가지 상황이 있다. `Dependencies lists` 와 `React.memo`가 바로 그 두가지다. `Dependencies lists` 먼저 살펴보자.
```js
function Foo({bar, baz}) {
  React.useEffect(() => {
    const options = {bar, baz}
    buzz(options)
  }, [bar, baz])
  return <div>foobar</div>
}

function Blub() {
  const bar = React.useCallback(() => {}, [])
  const baz = React.useMemo(() => [1, 2, 3], [])
  return <Foo bar={bar} baz={baz} />
}
```

위의 예제 코드에서 `bar`은 함수, `baz`는 배열이다. `Dependencies lists` 에 들어가는 것이 객체 또는 배열, 함수라면(불변하는 원시값이 아니라면) useCallback과 useMemo를 사용해서 참조해야한다.

> 원시 값: string, number, bigint, boolean, null, undefined, symbo

다음으로는 `React.memo`를 살펴보자.
```js
const CountButton = React.memo(function CountButton({ onClick, count }) {
  return <button onClick={onClick}>{count}</button>;
});
```

`React.memo` 를 사용해 렌더링 결과를 메모이징(Memoizing)함으로써, 불필요한 리렌더링을 건너뛴다.
props가 바뀔 때만 `CountButton`이 리랜더 된다.
같은 props로 렌더링이 자주 일어나는 컴포넌트에서 사용할 수 있다.

> 파라미터를 캐치해서 파라미터가 동일하면 컴포넌트의 결과값을 메모이제이션 해준다

#### Computationally expensive calculations (계산적으로 비용이 많이 드는 계산)

```js
function RenderPrimes({ iterations, multiplier }) {
  const primes = React.useMemo(() => calculatePrimes(iterations, multiplier), [
    iterations,
    multiplier
  ]);
  return <div>Primes! {primes}</div>;
}
```

* `useMemo` 의 경우에 적용되는 예시이다
* 소수를 계산하는 것 과 같은 비용이 큰 계산
* 매 랜더마다함수 prime들을 계산하도록 정의했더라도 값이 필요할때만 불러서 사용하게 될 것이다

## 마무리
useCallback과 useMemo 를 무분별하게 사용하는 것은 좋지 않다는 것을 알게 되었다. 이전에는 마냥 쓰면 좋겠지! 하고 사용했다면 이번 포스팅을 작성하면서는 조금 더 새로운 시각에서 이 들 hooks를 바라볼 수 있게 되어 기쁘다. 이들을 사용함으로써 발생하는 비용과, dependecies array 에서 발생할 수 있는 실수를 생각하고 잘 사용해보자! 