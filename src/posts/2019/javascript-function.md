---
title: JavaScript Function 정의와 호출
description: 반복적으로 사용하는 코드를 하나로 정리해주는 아름다운 함수
date: 2019-06-06
tags:
  - posts
layout: layouts/post.njk
---

함수를 선언하는 방법에는 여러가지가 있다. 이 글에서는 아래에 나와있는 7가지 방법중 오늘은 가장 많이 쓰이는 함수 선언문, 함수 표현식 그리고 화살표 함수식에 대해서 알아볼것이다.

> 1. 함수 선언문 (The function declaration)
> 2. 함수 표현식 (The function expression)
> 3. 제네레이터 함수 선언문 (The generator function declaration)
> 4. 제네레이터 함수 표현식 (The generator function expression)
> 5. 화살표 함수식 (The arrow function expression)
> 6. function 생성자 (The Function constructor)
> 7. 제네레이터 function 생성자 (The GeneratorFunction constructor)

## 함수 선언

함수 선언(function declaration)은 지정된 매개변수(parameter)를 갖는 함수를 정의한다. Function 생성자나 함수 표현식(function expression)을 사용해서 정의할 수도 있다. 함수 이름을 반드시 명시해야하고 호출할때는 함수의 이름으로 호출한다.

```javascript
function calcRectArea(width, height) {
  return width * height;
}

console.log(calcRectArea(5, 6)) // 30
```

기본적으로 함수는 undefined를 반환한다. 다른 값을 반환하기 위해서는, 함수는 반환값을 지정하는 return문이 있어야 한다. 함수 선언은 그 선언을 둘러싼 함수의 최상부나 전역 범위로 끌어올려진다(함수 호출을 함수 선언 전 또는 함수 선언 후에 할 수 있다). 그렇기 때문에 호이스팅에 영향을 받는다.

> 참조 : [MDN 함수 선언문](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/function)

## 함수 표현식

대부분의 자바스크립트 스타일 가이드에서는 함수 표현식을 사용할것을 권한다. 그도 그럴것이 함수 표현식은 호이스팅 되지 않기 때문이다. 함수 호출은 함수가 선언 된 후 에 해야하기 때문에 함수 선언문 처럼 정의 되기 전에 실행해버려 오류가 뜨는 일이 없을것이다.

```javascript
const getRectArea = function(width, height) {
    return width * height
}

console.log(getRectArea(3,4)) // 12

```

함수 표현식의 특징으로는 익명 함수를 만들 경우 함수 이름을 생략할 수 있다는 점, 함수 선언문과 달리 끌어올려지지 않는다는점, 클로져와 콜백으로 사용가능하다는 점이 있다.

> 참조 : [MDN 함수 표현식](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/function)

## 화살표 함수식

ECMA Script 6에서 새롭게 추가된 Arrow function은 내가 함수 표현식에 적용해 가장 많이 쓰는 함수 정의 방법이다. 보기에 간결하고, 읽기 쉽기 때문이다.

```javascript
const getTriangle = (base, height) => {
    return base * height / 2
}

console.log('삼각형의 면적: ' + getTriangle(5, 2))
```

> function이 파라미터값과 함수 본체를 연결해주는 `=>`로 표현된다.
본체가 한문장인 경우에는 블록을 나타내는 `{}`과 `return`명령이 생략가능하다.
그러면 위의 코드는 다음과 같이 바꾸어 쓸 수 있다.

```javascript
const getTriangle = (base, height) => base * height / 2
```

> 너무 짜릿하다! Arrow function 최고야.
게다가 인수가 한 개인 경우에는 인수를 감싸고 있는 괄호도 생략할 수 있다. 이 예를 들기 위해 원의 면적을 구하는 함수를 작성해 보겠다.

```javascript
const getCircle = radius => radius * radius * Math.PI
```

하지만 이런 아름다운 Arrow function을 행복하게 쓰려면 주의해야할 점이 몇가지 있다.

1. 익명함수 즉 리터럴 함수로만 정의할 수 있다. 다른곳에 전달하려 할 때 가장 유용하게 쓸 수 있다. 배열 검색을 할 때 등에서 콜백 함수로 쓸 수 있다.

```javascript
const index = notes.findIndex((note, index) => note.title === 'My favorite food')
```

2. `this`가 다른 변수와 마찬가지로 정적으로 (lexcially) 묶인다. Arrow function을 사용하면 내부 함수 안에서 this를 사용할 수 있다.

> this에 대한 내용은 [화살표 함수 - 3.this](https://poiemaweb.com/es6-arrow-function) 문서에 자세하게 나와있다.

3. 함수 정의 방법 중 하나인 객체 생성자로 사용할 수 없다.
