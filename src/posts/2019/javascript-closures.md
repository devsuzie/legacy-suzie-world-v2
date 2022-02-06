---
title: Closures
description: 클로저는 무엇이며 왜 쓰는것일까
date: 2019-08-16
tags:
  - posts
layout: layouts/post.njk
---

클로저.. 헷갈린다. 뭔지는 알겠는데 그래서 뭐냐고 물으면 뭐라고 말해야할지 망설이게 된다. 그말은 즉슨 헷갈린다는거다. [What is a closure](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)이라는 글에서는 클로저에 대해서 아래처럼 말한다.

> 클로저에 대한 이해 없이 자바스크립트 코딩을 하는것은 문법에 대한 이해 없이 영어로 말을 하는것과 같다. 무슨 말을 하는지 전달은 할 수 있겠지만 이상하게 들릴것이다. 


## 클로저
그렇다면 클로저는 무엇일까? 클로저는 외부 함수가 반환된 후에도 외부 함수의 변수 범위 체인에 접근할 수 있는 함수이다. 클로저는 내부 함수에서부터 외부함수의 스코프에 접근할 수 있게 해준다. 자신을 포함하고 있는 외부함수보다 내부함수가 더 오래 유지되는 경우, 외부 함수 밖에서 내부함수가 호출되더라도 외부함수의 지역 변수에 접근할 수 있게 해준다. MDN 정의를 살펴보자

> 클로저는 함수와 그 함수가 선언됐을 때의 렉시컬 환경(Lexical environment)과의 조합이다.

위 정의에서 말하는 `함수`란 반환된 내부 함수이고, `그 함수가 선언됐을 때의 렉시컬 환경`은 내부 함수가 선언됐을 때의 스코프를 말한다.

```javascript
function outerFunc() {
  var x = 10;
  var innerFunc = function () { console.log(x); };
  innerFunc();
}

outerFunc(); // 10
```

클로저는 반환된 내부함수가 자신이 선언됐을 때의 스코프를 기억하여 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수다. 위 코드에서 `outerFunc`함수의 변수 `x`를 자유변수(Free variable)라고 부른다. 자유변수는 클로저로 참조되는 외부 변수이다.

## 클로저의 활용

클로저를 언제 쓸 수 있는지에 대해서 알아보자. 아래에 나오는 예시는 [piemaweb - 5.19 클로저](https://poiemaweb.com/js-closure)를 참고했다. 전체 코드와 실행되는 모습은 링크에 들어가면 자세히 나와있으니 들어가서 살펴보자.

### 상태 유지
클로저는 현재 상태를 기억하고 변경된 최신 상태를 유지할 수 있다. 아래 코드에서는 변수 `isShow`는 클로저가 기억하는 렉시컬 환경의 변수이므로 소멸하지 않고 현재 상태를 기억한다. 

```javascript
var toggle = (function () {
  var isShow = false;

  return function () {
    box.style.display = isShow ? 'block' : 'none';
    isShow = !isShow;
  };
})();

toggleBtn.onclick = toggle;
```

위의 코드에 나오는 괄호 두개 `()()`는 내가 이전글에도 말한적이 있는 즉시 실행 함수 (Immediately-invoked function expression)이다. 
말그대로 함수를 정의하고 나서 바로 실행하는것이다. 클로저를 여러개 생성할 필요 없이 하나만 생성해서 사용할 경우에 유용하다. 
IIFE는 함수를 반환하고 즉시 소멸되지만 위의 코드에서는 클로저를 반환하기 때문에 계속해서 함수 안에서 선언된 자유변수에 접근할 수 있다.
또한 즉시 실행함수이므로 `toggle()`이 아닌 `toggle`로 부른 모습을 볼 수 있다.

### 전역 변수의 사용 억제
위에서 살펴본 `상태 유지`와 함께 살펴볼 클로저의 활용은 전역 변수의 사용 억제이다. 
코드를 쓸 때 전역 변수를 사용하는것은 지양해야할 부분 중 하나이다. 전역 변수를 사용하게 되면 다른 누군가가 실수로 덮어 쓸 위험이 있기 때문이다. 
그래서 변수를 선언할때에는 해당 변수가 필요한 함수내에서 클로저를 사용해 선언하는것이 좋다.

```javascript
var increase = (function () {

  var counter = 0;

  return function () {
      return ++counter;
  };
}());

incleaseBtn.onclick = function () {
  count.innerHTML = increase();
};
```

변수 `increase`에서 리턴되는 함수 `++counter`은 클로저다. 클로저인 이 함수는 렉시컬 환경에 있는 변수인 `counter`의 현재 상태를 기억하고 변경된 최신 상태를 유지한다.
즉시 실행 함수는 한번만 실행되므로 호출될 때마다 변수 `counter`가 0으로 초기화 되지 않는다.


### 프라이빗 메소드 흉내내기
자바스크립트에서는 클로저를 이용해 `프라이빗 메소드`를 흉내내는 것이 가능하다. 프라이빗 메소드는 말그대로 메소드를 프라이빗으로 선언해 코드에 제한적 접근을 허용해준다. 
아래 예시는 MDN에서 [클로저를 이용해서 프라이빗 메소드 흉내내기](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures#%ED%81%B4%EB%A1%9C%EC%A0%80%EB%A5%BC_%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C_%ED%94%84%EB%9D%BC%EC%9D%B4%EB%B9%97_%EB%A9%94%EC%86%8C%EB%93%9C_(private_method)_%ED%9D%89%EB%82%B4%EB%82%B4%EA%B8%B0)를 참고했다.

```javascript
var makeCounter = function() {
  var privateCounter = 0;

  function changeBy(val) {
    privateCounter += val;
  }

  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  }  
};

var counter1 = makeCounter();
var counter2 = makeCounter();
alert(counter1.value()); /* 0 */
counter1.increment();
counter1.increment();
alert(counter1.value()); /* 2 */
counter1.decrement();
alert(counter1.value()); /* 1 */
alert(counter2.value()); /* 0 */
```

makeCounter함수에는 `privateCounter`라는 지역변수와 `changeBy`라는 함수가 있다. 
둘 다 makeCounter외부에서는 접근할 수 없지만 makeCounter가 리턴하는 3개의 함수 (increment, decrement, value)에 의해 공유되는 하나의 렉시컬 환경을 만들어 접근할 수 있다.
변수 `counter1` 그리고 `counter2`를 만들어보자. 각 카운터가 호출될 때마다 하나의 클로저에서 변수 값을 변경해도 다른 클로저 값에는 영향을 주지 않는 모습을 볼 수 있다.
이런 방식으로 클로저를 사용하여 객체지향 프로그래밍의 정보 은닉과 캡슐화 같은 이점들을 얻을 수 있다.
또한 이렇게 클로저를 사용하는 것을 `모듈 패턴`이라고 한다.

## 마무리
함수 내부에 함수를 작성할 때마다 클로저는 생성된다. 내부에 작성된 함수가 바로 클로저이다. 위의 예시들을 다 이해하고 나서야 비로서 그 말이 무슨 말인지 알게 되었다. 클로저는 `객체 데이터 프라이버시`, `이벤트 핸들러`, `콜백 함수`, `partial app`, `currying` 그리고 함수형 프로그래밍 패턴에서 사용된다. 이제 클로저를 알게 되었으니 자바스크립트라는 언어로 코드를 작성하는 즐거움을 더 잘 느낄 수 있을 것 같아 행복하다.