---
title: React Hooks를 사용해 setInterval 선언 만들기
description: Dan Abramov의 블로그 포스팅을 번역한 글입니다
date: 2020-07-04
tags:
  - Programming
layout: layouts/post.njk
------

> 이 글은 Dan Abramov가 작성한 [Making setInterval Declarative with React Hooks](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)의 번역문입니다. 원작자의 허락을 구한 후 번역하였습니다.

## Making setInterval Declarative with React Hooks

[ReactHooks](https://reactjs.org/docs/hooks-intro.html)를 몇 시간 이상 다뤄봤다면, 당신은 아마도 흥미로운 문제에 부딪혔을 것이다. 바로 `setInterval`이 당신이 기대한 대로 [동작하지 않는다](https://stackoverflow.com/questions/53024496/state-not-updating-when-using-react-state-hook-within-setinterval)는 문제 말이다 .

[Ryan Florence의 말](https://mobile.twitter.com/ryanflorence/status/1088606583637061634)에 의하면:

> 나는 hooks와 setInterval이 리액트의 오점이라고 지적하는 많은 사람을 만났다.

솔직하게 말하자면, 그 사람들이 제대로 지적했다고 생각한다. 처음에는 분명 혼란스럽다.

하지만 나는 이것을 Hooks의 오점이 아니라, [리액트 프로그래밍 모델](https://overreacted.io/react-as-a-ui-runtime/)과 `setInterval` 사이의 부조화라고 생각한다. Hooks는 클래스보다 리액트 프로그래밍 모델에 가까우므로 이러한 부조화가 더 두드러져 보인다.

**이들이 함께 잘 동작할 수 있도록 하는 방법이 있긴 하지만 직관적이지는 않다.**

이 게시물에서 우리는 어떻게 하면 setInterval과 Hooks가 잘 어울릴 수 있는지, 왜 이 해결책이 말이 되는지, 그리고 어떤 새로운 기능을 제공할 수 있는지 살펴볼 것이다.

**고지사항: 이 게시물은 임상 케이스에 초점을 두고 있다. API가 백 가지의 사용 사례를 단순화하더라도, 논의는 항상 더 복잡한 사례에 초점을 둘 것이다.**

만약 당신이 Hooks를 처음 접했고 어떤 일에 대한 것인지 이해하지 못한다면, [이 소개 글](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)과 [이 문서](https://reactjs.org/docs/hooks-intro.html)를 대신 읽어보기를 추천한다. 이 게시물은 당신이 Hooks를 사용해서 한 시간 이상 작업해본 적이 있다는 가정하에 쓰였다.

## 그냥 코드를 보여줘
긴 말 없이, 여기에 매 초마다 증가하는 카운터가 있다:

```js
import React, { useState, useEffect, useRef } from 'react';

function Counter() {
  let [count, setCount] = useState(0);

  useInterval(() => {
    // Your custom logic here
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>;
}
```

([CodeSandbox 데모](https://codesandbox.io/s/105x531vkq))

이 `useInterval`은 내장된 리액트 훅이 아닌 내가 작성한 [커스텀 훅](https://reactjs.org/docs/hooks-custom.html)이다:

```js
import React, { useState, useEffect, useRef } from 'react';

function useInterval(callback, delay) {
  const savedCallback = useRef();

  // Remember the latest callback.
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Set up the interval.
  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    if (delay !== null) {
      let id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}
```

(혹시 아까 놓쳤다면 여기에 [CodeSandbox 데모](https://codesandbox.io/s/105x531vkq)가 있다.)

**내가 만든  `useInterval` 훅은 간격을 설정하고 unmounting 이후에 정리한다.** 
이것이 컴포넌트 생명 주기에 묶인 `setInterval`과 `clearInterval` 콤보다. 

자유롭게 당신의 프로젝트나 npm에 복사 붙여넣기 하여 사용해도 좋다.

**만약 이것이 어떻게 동작하는지 신경 쓰지 않는다면, 여기까지만 읽어도 된다. 나머지 게시물은 React Hooks에 심층적으로 접근할 준비가 된 사람들을 위한 것이다.**

## 잠시만, 뭐라고?
지금 당신이 무슨 생각하고 있을지 안다: 

> 단, 이 코드는 말이 되지 않아. “Just Javascript”에 무슨 일이 일어난 거야? 리액트 훅스가 상어를 뛰어넘었다는 걸 인정해! ([Jump the shark](https://www.urbandictionary.com/define.php?term=jump-the-shark): 어떤 것이 종말의 길을 걷기 시작했다는 뜻의 관용어로, 텔레비전 시리즈에서 유래되었다.)

**나도 그렇게 생각했지만 내 생각을 바꿨고, 나는 당신의 생각도 바꿀 것이다.** 이 코드가 왜 말이 되는지 설명하기 전에, 이 코드로 어떤 일을 할 수 있는지 보여주고 싶다.

## 왜 `useInterval()`이 더 나은 API인가

내 `useInterval`훅은 함수와 지연을 받아오고 있다는 사실을 짚고 가자:

```js
useInterval(() => {
  // ...
}, 1000);
```

이 훅은 `setInterval`과 많이 닮았다:

```js
setInterval(() => {
  // ...
}, 1000);
```

그러면 왜 `setInterval`을 직접 사용하지 않을까?

이 사실은 처음에는 분명하지 않을 수도 있지만, 당신이 알고 있는 `setInterval`과 내 `useInterval` 훅의 차이점은 바로 **arguments가 “동적”이라는 점이다.**

나는 이 점을 구체적인 예시로 설명하겠다.

간격 지연을 조정할 수 있도록 하고 싶다고 가정해보자.

![counter_delay](https://overreacted.io/35e4f35a8585255b11c090aed9f72433/counter_delay.gif)

당신이 꼭 input tag로 지연을 제어하지는 않겠지만, 사용자가 다른 탭으로 전환한 동안 일부 AJAX 업데이트에 대해 폴링을 적게 하는 등 - 지연을 동적으로 조정하는 것은 유용할 수 있다.

이 `setInterval`을 클래스에서는 어떻게 구현할 것인가? 나는 이러한 결론에 봉착했다:

```js
class Counter extends React.Component {
  state = {
    count: 0,
    delay: 1000,
  };

  componentDidMount() {
    this.interval = setInterval(this.tick, this.state.delay);
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevState.delay !== this.state.delay) {
      clearInterval(this.interval);
      this.interval = setInterval(this.tick, this.state.delay);
    }
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  tick = () => {
    this.setState({
      count: this.state.count + 1
    });
  }
  handleDelayChange = (e) => {
    this.setState({ delay: Number(e.target.value) });
  }

  render() {
    return (
      <>
        <h1>{this.state.count}</h1>
        <input value={this.state.delay} onChange={this.handleDelayChange} />
      </>
    );
  }
}
```

([CodeSandBox 데모](https://codesandbox.io/s/mz20m600mp))

나쁘지 않지?

Hook에서는 어떤 모습일까?

🥁🥁🥁 (두구두구두구)

```js
function Counter() {
  let [count, setCount] = useState(0);
  let [delay, setDelay] = useState(1000);

  useInterval(() => {
    // Your custom logic here
    setCount(count + 1);
  }, delay);
  function handleDelayChange(e) {
    setDelay(Number(e.target.value));
  }

  return (
    <>
      <h1>{count}</h1>
      <input value={delay} onChange={handleDelayChange} />
    </>
  );
}
```

([CodeSandBox 데모](https://codesandbox.io/s/mz20m600mp))

그렇다, 이게 전부다.

클래스와 달리, `useInterval` 훅을 “업그레이드” 하여 동적으로 지연 간격을 조정하는 복잡함이 없다:

```js
// Constant delay
useInterval(() => {
  setCount(count + 1);
}, 1000);
// Adjustable delay
useInterval(() => {
  setCount(count + 1);
}, delay);
```

`useInterval`이 다른 delay를 캐치하면 다시 delay를 가져오거나 설정한다.

**interval을 설정하고 해제하는 코드를 작성하는 대신, 특정한 delay와 함께 interval을 선언했다. 그리고 우리의 `useInterval` 훅은 이를 실현시켰다.**

만약에 내가 interval을 일시적으로 정지하기를 원한다면 어떨까?  이 또한 state를 사용해서 해결할 수 있다:

```js
const [delay, setDelay] = useState(1000);
const [isRunning, setIsRunning] = useState(true);

useInterval(() => {
  setCount(count + 1);
}, isRunning ? delay : null);
```

([데모](https://codesandbox.io/s/l240mp2pm7) 여기 있음!)

이것이 나를 Hooks 그리고 React에 대해 흥분되게 하는 점이다. 우리는 기존의 명령적 API를 포장하고 우리의 의도와 가깝게 표현하는 선언적 API를 만들 수 있다.

이것으로  `useInterval()`훅이 더 멋진 API라는 사실을 당신이 납득했기를 바란다. 

하지만 왜 `setInterval()`과 `clearInterval()`을 사용하는 것은 Hooks를 짜증 나게 만들까? 우리의 카운터 예제로 돌아가 순서대로 실행해보자.


## 첫번째 시도

초기 state를 렌더하는 간단한 예시로 시작해보자:

```js
function Counter() {
  const [count, setCount] = useState(0);
  return <h1>{count}</h1>;
}
```

이제 나는 매 초마다 증가하는 Interval을 원한다. 이건 [cleanup이 필요한 side effect](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup)이다. 그래서 나는 `useEffect()`를 사용해서 정리하는 cleanup 함수를 리턴할것이다:

```js
function Counter() {
  let [count, setCount] = useState(0);

  useEffect(() => {
    let id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  });
  return <h1>{count}</h1>;
}
```

([CodeSandbox 데모](https://codesandbox.io/s/7wlxk1k87j))

쉬워 보이지 않는가? 이런 식으로 작동하는 것이다.

**하지만, 이 코드는 뭔가 이상하다.**

리액트는 매 랜더 이후에 effect를 기본적으로 재적용한다. 이것은 의도적이고 현재 리액트 클래스 컴포넌트에서 발생하는 모든 종류의 [버그](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)를 피하는 데 도움이 된다.

이것은 많은 구독 API가 예전 것을 지우고 새로운 listenr를 언제든지 추가할 수 있기 때문에 꽤 좋다. 하지만 `setInterval`은 이 중 하나가 아니고 우리가 `clearInterval`과 `setInterval` 을 실행할 때, 이 타이밍이 변한다. 만약에 너무 자주 리렌더링 하고 effec를 재적용하면, 이 interval은 절대 작동할 기회를 얻지 못할 것이다.

더 작은 interval 안에서 컴포넌트를 리 랜더해보면 버그를 확인할 수 있다:

```js
setInterval(() => {
  // Re-renders and re-applies Counter's effects
  // which in turn causes it to clearInterval()
  // and setInterval() before that interval fires.
  ReactDOM.render(<Counter />, rootElement);
}, 100);
```

(이 버그의 [데모](https://codesandbox.io/s/9j86r218y4))

---

## 두번째 시도
당신은 아마도 `useEffect()` 를 사용하면 재적용 효과를 [최적화](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects) 할 수 있다는 사실을 알고 있을 것이다. 의존성 배열을 두 번째 인수로 지정할 수 있으며, 리액트는 배열 안에 포함된 내용이 변경된 경우에만 다시 실행할 것이다.

```js
useEffect(() => {
 document.title = `You clicked ${count} times`;
}, [count]);
```

이 효과를 mount 시점과, unmount 시점에서 정리(clean-up)할 때만 실행하고 싶다면 빈 배열을 넘기면 된다.

하지만, 만약 자바스크립트의 클로저 개념에 익숙하지 않다면 이건 흔한 실수의 원천이 된다. 이러한 실수를 바로 잡아보자! (이러한 버그를 조기에 발견하기 위한 [lint rule](https://www.npmjs.com/package/eslint-plugin-react-hooks)이 있다.)

첫 번째 시도에서, 우리의 문제는 effects를 재실행하는 것이 타이머를 너무 일찍 정리하는 원이 되었다는 것이었다. 재실행하지 않음으로써 이 문제를 해결할 수 있다:

```js
function Counter() {
  let [count, setCount] = useState(0);

  useEffect(() => {
    let id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <h1>{count}</h1>;
}
```

하지만, 이제 우리 카운터는 1로 업데이트 된 이후에 꼼짝도 하지 않게 되었다. ([이 버그의 데모](https://codesandbox.io/s/jj0mk6y683).)

무슨 일이 일어난 걸까?!

**문제는 `useEffect`가 `count`를 첫 번째 렌더에서 캡처했다는 사실이다.** 이것은 0과 같다. 우리는 절대 다시 효과를 적용하지 않고, 그래서 `setInterval`에 있는 클로저는 항상 첫 번째 랜더에서 온 `count`를 참조하기 때문에  `count+1`은  항상 `1`일 것이다. 이럴 수가!

**네 이빨이 갈리는 소리가 들리는데.. 훅은 너무 짜증 나, 맞지?**

이를 해결하는 [한 가지 방법](https://codesandbox.io/s/j379jxrzjy)은 `setCount(count + 1)`를 `setCount(c => c + 1)` 과 같은 “updater”형식으로 교체하는 것이다. 이렇게 하면 이 변수로부터 항상  최신 state를 읽을 수 있다. 하지만 이것은 최신 props를 읽는 데는 도움이 되지 않는다.

[또 다른 해결책](https://codesandbox.io/s/00o9o95jyv)은 `useReducer()`를 사용하는 것이다. 이 접근방식은 우리에게 더 많은 유연함을 제공한다. 리듀서 안에서, 최신 state와 props 둘 다에 접근할 수 있다. `dispatch` 기능 자체는 변하지 않기 때문에 어떤 클로저에서든 데이터를 가져올 수 있다. `useReducder()`의 한계는 그 안에서 side effects를 보낼 수 없다는 것이다. (하지만, 어떤 effect를 불러일으키는 새로운 state를 돌려보낼 수 있다)

그런데.. 왜 이렇게 난잡해지는 것 같지?

## The Impedance Mismatch
이 용어는 때때로 사용되는데, [Phil Haack](https://haacked.com/archive/2004/06/15/impedance-mismatch.aspx/)는 이렇게 설명한다:

> 어떤 사람은 데이터베이스는 화성에서, 오브젝트는 금성에서 왔다고 말한다. 데이터베이스는 오브젝트 모델에 자연스럽게 매핑되지 않는다. 그건 두 개의 자석을 북쪽으로 마주 보게 하려는 것과 같다.

우리의 “impedance 불일치”는 데이터베이스와 오브젝트 얘기가 아니다. 리액트 프로그래밍 모델과 명령형 `setInterval` API 사이에 있다.

**리액트 컴포넌트는 잠깐 마운트 되고 여러 가지 다양한 상태를 거칠 수 있지만, 이 랜더 결과는 한 번에 모든 것을 설명한다.**

```js
// Describes every render
return <h1>{count}</h1
```

Hooks는 효과에 동일한 선언적 접근을 할 수 있게 한다:

```js
// Describes every interval state
useInterval(() => {
  setCount(count + 1);
}, isRunning ? delay : null);
```

우리는 간격을 설정하지 않고 설정 여부와 지연 시간을 명시한다. 우리의 훅이 이것을 가능하게 한다. 연속적인 과정은 별개의 용어로 설명된다.

**반면, `setInterval`은 과정을 시간 단위로 명시하지 않는다. - 네가 한번 간격을 설정하면, 정리하는 것 외에는 변경할 수 있는 게 없다.**

이것이 바로 리액트 모델과 `setInterval` API의 불일치이다.

리액트 컴포넌트에서 props와 state는 변할 수 있다. 리액트는 이들을 리 랜더 할 것이고 이전의 랜더 결과에 대해서는 “잊어버릴 것”이다. 전혀 무관해지게 된다.

`useEffect()` 훅 또한 이전의 랜더 결과를 “잊는다”. 마지막 효과를 정리하고 다음 효과를 설정한다. 다음 효과는 신선한 props와 state 위에 닫힌다. 이것이 우리의 첫 번째 시도가 간단한 경우에는 효과가 있었던 이유이다.

반면에, `setInterval()`은 “잊지 않는다”. 이것은 당신이 교체할 때까지 - 시간을 다시 설정하지 않고는 할 수 없는 - 이전의 props와 상태를 영원히 참조할 것이다.

아니다, 시간을 다시 설정할 수 있나? 

## Refs가 구해줄거야!
문제는 이렇게 요약된다:

- 첫 번째 랜더에서 `callback1`로 `setInterval(callback1, delay)`을 한다.
- 우리는 새로운 props와 state을 감싸는 `callback2`를 다음 랜더로부터 가진다.
- 하지만 우리는 시간을 다시 설정하지 않고는 이미 존재하는 간격을 대체할 수 없다!

**그렇다면 간격을 아예 바꾸지 않고, 대신에 최신 간격 콜백을 가리키는 변경 가능한 `savedCallback` 변수를 도입하면 어떨까?**

이제 해결할 수 있을 것 같다:

- 우리는 `fn`이  `savedCallback`을 호출하는 곳에서  `setInterval(fn, delay)`를 실행한다
- 첫 번째 랜더 이후에  `savedCallback`을 `callback1`로 설정한다
- 다음 렌더링 후 `savedCallback`을 `callback2`로 설정한다
- ???
- PROFIT

변경 가능한 `savedCallback`은 리랜더 전체에 걸쳐 “지속”할 필요가 있다. 그래서 정규 변수가 될 수 없다. 우리는 인스턴스 필드와 같은 것을 원한다.

우리가 [Hooks FAQ](https://reactjs.org/docs/hooks-faq.html#is-there-something-like-instance-variables)에서 알 수 있듯이, `useRef()`는 정확히 다음을 제공한다:

```js
const savedCallback = useRef();
// { current: null }
```

(리액트의 [DOM refs](https://reactjs.org/docs/refs-and-the-dom.html)에 익숙할 수 있다. Hooks는 모든 변경가능한 값을 가지는데 동일한 개념을 사용한다. ref는 무엇이든 넣을 수 있는 “상자”와 같다.)

`useRef()`는 렌더 간에 공유되는 변경가능한 현재 속성이 있는 일반 객체를 반환한다. 우리는 여기에 최신 간격 콜백을 저장할 수 있다.

```js
function callback() {
   // Can read fresh props, state, etc.
   setCount(count + 1);
 }

 // After every render, save the latest callback into our ref.
 useEffect(() => {
   savedCallback.current = callback;
 });
```

그리고 나서 우리의 interval 안에서 읽거나 불러올 수 있다.

```js
useEffect(() => {
  function tick() {
    savedCallback.current();
  }

  let id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

빈 배열 `[]` 덕분에, 이 효과는 두번다시 실행되지 않고, interval은 리셋되지 않는다. 하지만 `savedCallback` ref 덕분에, 우리는 항상 마지막 랜더 이후에 설정한 콜백을 읽을 수 있고, interval 체크 표시를 통해 콜백을 호출할 수 있다.

여기에 완성된 코드가 있다:

```js
function Counter() {
  const [count, setCount] = useState(0);
  const savedCallback = useRef();

  function callback() {
    setCount(count + 1);
  }

  useEffect(() => {
    savedCallback.current = callback;
  });

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }

    let id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;
}

```

## 훅 추출하기
물론, 위 코드는 혼란스러울 수 있다. 반대되는 패러다임을 함께 사용하기란 혼란스럽다. 변경 가능한 refs를 엉망으로 만들 가능성 또한 있다.

**나는 Hooks가 클래스보다 낮은 단계의 원시적 요소를 제공한다고 생각한다. 하지만 그것의 아름다움은 우리가 더 나은 선언적 추상적 개념을 구성하고 창조할 수 있게 해준다는 것이다.**

나는 그냥 이렇게 쓰고 싶다:

```js
function Counter() {
  const [count, setCount] = useState(0);

  useInterval(() => {
    setCount(count + 1);
  }, 1000);
  return <h1>{count}</h1>;
}
```

내가 만든 ref의 내용을 복사해서 커스텀 훅에 붙여넣기 해보겠다.

```js
function useInterval(callback) {
  const savedCallback = useRef();

  useEffect(() => {
    savedCallback.current = callback;
  });

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }

    let id = setInterval(tick, 1000);
    return () => clearInterval(id);
  }, []);
}
```

지금은, 지연 시간 `1000`이 하드코딩 되어있다. 나는 이것을 인자로 넘기기를 원한다:

```js
function useInterval(callback, delay) {
```

interval을 설정할 때 이렇게 쓸 것이다:

```js
let id = setInterval(tick, delay);
```

이제 랜더 안에서 `delay`는 변경 가능하고, interval effect의 배열 안에 이걸 정의 해줘야한다.

```js
useEffect(() => {
  function tick() {
    savedCallback.current();
  }

  let id = setInterval(tick, delay);
  return () => clearInterval(id);
}, [delay]);
```

잠깐, 우리는 간격 효과의 재설정을 피하고 싶었고, 그것을 피하기 위해 특별히 `[]`를 전달하지 않았는가? 그런건 꼭 아니다. 콜백 변경 시 재설정되는 것을 피하고 싶었을 뿐이다. 하지만 지연이 바뀌면 타이머를 다시 시작하고 싶다!

코드가 작동하는지 확인해보자:

```js
function Counter() {
  const [count, setCount] = useState(0);

  useInterval(() => {
    setCount(count + 1);
  }, 1000);

  return <h1>{count}</h1>;
}

function useInterval(callback, delay) {
  const savedCallback = useRef();

  useEffect(() => {
    savedCallback.current = callback;
  });

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }

    let id = setInterval(tick, delay);
    return () => clearInterval(id);
  }, [delay]);
}
```

([CodeSandbox 데모](https://codesandbox.io/s/xvyl15375w))

동작한다! 우리는 이제 `useInterval()`을 어떤 컴포넌트에서든 사용할 수 있고 이것의 구현 세부사항에 대해서는 깊게 생각하지 않아도 된다.

---

## 보너스: Interval 일시정지

`null`을 `delay`로 전달하여 interval을 일시 중지하려면 어떻게 해야할까:

```js
const [delay, setDelay] = useState(1000);
const [isRunning, setIsRunning] = useState(true);

useInterval(() => {
  setCount(count + 1);
}, isRunning ? delay : null);
```

이걸 어떻게 실행할 수 있을까? 정답은: interval을 설정하지 않음으로써 그렇게 할 수 있다.

```js
useEffect(() => {
  function tick() {
    savedCallback.current();
  }

  if (delay !== null) {
    let id = setInterval(tick, delay);
    return () => clearInterval(id);
  }
}, [delay]);
```

([CodeSandbox](https://codesandbox.io/s/l240mp2pm7) 데모)

이게 전부다. 이 코드는 지연, 일시 중지 또는 interval 재개 등 가능한 모든 전환을 처리한다. `useEffect()` API는 우리에게 설정과 정리에 대해 설명하기 위해 더 많은 초기 노력을 기울이도록 요구하지만 새로운 케이스를 추가하는건 쉽다.

---

## 보너스: 재밌는 데모
이 `useInterval()` 훅을 가지고 놀면 꽤 재밌다. side effect가 정의되었을 때, 복잡한 행동을 함께 조정하기가 훨씬 쉬워진다.

예를 들어서, 한 간격의 지연이 다른 간격에 의해 제어되도록 할 수 있다:

![counter_inception](https://overreacted.io/10cfc4b38497a46980d3a13048a56e36/counter_inception.gif)

```js
function Counter() {
  const [delay, setDelay] = useState(1000);
  const [count, setCount] = useState(0);

  // Increment the counter.
  useInterval(() => {
    setCount(count + 1);
  }, delay);

  // Make it faster every second!
  useInterval(() => {
    if (delay > 10) {
      setDelay(delay / 2);
    }
  }, 1000);
  function handleReset() {
    setDelay(1000);
  }

  return (
    <>
      <h1>Counter: {count}</h1>
      <h4>Delay: {delay}</h4>
      <button onClick={handleReset}>
        Reset delay
      </button>
    </>
  );
}
```

([CodeSandbox 데모](https://codesandbox.io/s/znr418qp13))

## 마무리
Hooks는 익숙해지는 데 시간이 걸린다 - 그리고 특히 명령적이고 선언적인 코드의 경계에서 더 그렇다. 이것들로 [React Spring](https://www.react-spring.io/docs/hooks/basics)같은 강력한 선언적 추상화를 만들 수 있지만, 분명 신경 써야 할 것들이 있다.

지금 Hooks는 아직 초기 단계이고 (이 글은 2019년 2월에 쓰였다) , 아직 우리가 연구하고 비교해야 할 패턴들이 분명히 있다. 잘 알려진 “best practice”를 따르는 데 익숙하다면 Hooks를 서둘러 쓰지 않아도 된다. 아직도 많은 시도와 발견이 있다.

이 글이 Hooks와 setInterval()과 같은 API를 사용하는 것과 관련된 어려움을, 그리고 이를 극복하는 데 도움이 될 수 있는 패턴과 API를 만드는 것을 이해하는 데 도움이 되었기를 바란다.