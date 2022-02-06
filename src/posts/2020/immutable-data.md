---
title: Immutable Data
description: 자바스크립트의 불변성 그리고 Immutable.js 라이브러리
date: 2020-07-28
tags:
  - Programming
layout: layouts/post.njk
------

Immutable. 한국어로는 불변. 데이터가 불변하지 않는 게 왜 중요할까? 불변 객체라는 말은 여러 번 들어왔지만, 그 중요성에 대해서는 생각해본 적이 없었다. 이번 포스팅을 통해서 `Immutable` 데이터는 무엇이길래 그토록 중요한지, 그리고 `Immutable.js` 라이브러리는 무엇인지 알아보려고 한다.

## Immutable의 정의
MDN에서 말하는 [Immutable](https://developer.mozilla.org/en-US/docs/Glossary/Immutable)의 정의에 대해서 먼저 알아보자.

Immutable object는 내용을 변경할 수 없는 객체를 말한다. 객체는 다음과 같은 이유로 Immutable 할 수 있다:

- 성능 향상을 위해 (객체가 나중에 변경되지 않을 것이므로)
- 메모리 사용을 줄이기 위해 (전체 객체를 복제하는 대신 객체를 참조)
- Thread Safe (멀티 스레드 프로그래밍에서 같은 객체를 다른 객체의 간섭없이 참조)

## JavaScript Immutability
아래는 Immutability에 대해 찾아보다 발견한 생활코딩 이고잉님의 수업 [JavaScript Immutability](https://opentutorials.org/module/4075)를 듣고 정리한 내용이다.

immutability란 데이터의 원본이 훼손되는 것을 막는 것이다. 모든 데이터 작업에서는 생성과 읽기가 중요한데 이것이 바로 원본이기 때문이다. 우리의 앱에서 변경될 필요가 없는 부분을 확실히 냉동시켜두는 것이 좋을 것이다.

> 나만 해도 그렇고, 대부분 사람은 당연히 모든 데이터는 수정과 삭제가 가능한 거 아닌가? 라고 생각하지만, 보안상의 문제에서 그것이 당연하지 않은 경우가 있다! (예: 블록체인)

### 변수 할당 방식 
먼저 변수 할당 방식에 대해 알아보자. 변수가 어떤 방식으로 값을 가리키는가? 데이터 타입에는 원시 데이터 타입, 객체 데이터 타입 두 가지가 있다. 원시 데이터 타입에는 숫자, 문자, 불리언, null, undefined, symbol이 있고, 객체 데이터 타입에는 객체, 배열, 함수가 있다. 

아래의 코드 예제를 살펴보자.

```js
const p1 = 1
const p2 = 1

p1 === p2 //true

const o1 = {name: "kim"}
const o2 = {name: "kim"}

o1 === o2 //false
```

1은 1이고 이는 원시 데이터인 숫자이기 때문에 불변한다고 할 수 있다. 하지만 객체는 바뀔 수 있는 가능성이 있기 때문에 현시점에서 같은 값을 가진 객체라고 하더라도 따로 생성해서 보관한다.

### 객체의 복사
원본 데이터를 건들지 않고 수정하고 싶은 값만 건들 수 있으면 좋겠다는 생각이 든다면, 복제본을 만들어서 복제본을 변경하면 된다!

```js
const o1 = {name: "kim"}
const o2 = o1
o2.name = "lee"
```
위처럼 하면 `o1`의 프로퍼티가 변경되는데, 이는 우리가 원하는 것이 아니다.

```js
const o1 = {name: "kim}
const o2 = Objec.assign({}, o1)
o2.name = "lee"
```
`o1`을 복제해 `o2`를 만들었기 때문에 복제본은 `o1`에 영향을 미치지 않는다. `o1`값은 이제 불변하다고 할 수 있다. 우리는 원본을 Immutable 하게 유지할 수 있다.

> 객체의 속성을 복사할 때 쓰는 [Object.assign()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 메서드 

### 중첩된 객체
`Object.assign`을 통해서 복제하면 obejct의 property만 복제가 된다. property 중에 value의 데이터 타입이 객체형인 경우에는 값을 복사하는 것이 아니라 위치 복사를 복사하게 된다. 즉, `o2` 를 객체 복사했으니까 `o1`에는 영향이 가지 않을 거라고 생각할 수 있지만, 영향이 간다!

```js
const o1 = {name: "kim", score: [1, 2]}
const o2 = Object.assign({}, o1}
o2.score.push(3)
```

`o1` 객체를 `Object.assign` 메서드를 사용해서 복제했으니 `o2`의 score 값을 변화시킨게 `o1`에는 영향이 가지 않겠지? 라고 생각했다면 아래의 코드에서 결과를 확인해보자.

```
console.log(o1) // {name: "kim", score: [1,2,3]
console.log(o2) // {name: "kim", score: [1,2,3]
```

property중 value의 타입이 객체인 경우에는 값을 복사하는것이 아니라 위치를 복사하는 것이므로 score 값은 변했고 그 변한 score의 위치를 복사했기 때문에 둘 다 변했다!

`o2`만 바꾸고 싶으면 어떻게 해야할까. `concat`을 써서 한번 해보자.

```js
o2.score = o2.score.concat()
o2.score.push(3) // 복제된 놈으로 
```

`push`는 원본을 바꾸지만 `concat`은 원본을 복제한다. 배열의 경우에는 복제해주는 명령을 사용하면 된다. 그 이후에 push를 하면 `o1`이 가진 스코어 (원본)을 건들지 않는다. 이로써 우리는 원본의 Immutability를 지켰다!

> 배열의 원본을 복제하는 [Array.prototype.concat()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat) 메서드

### 불변 함수 만들기
```js
function fn(person) {
	person = Object.assign({}, person);
	person.name = "lee";
	return person;
}
var o1 = {name: "kim"}
var o2 = fn(o1);

console.log(o1); // {name: "kim"}
console.log(o2); // {name: "lee"}
```

함수 내에서 복제본을 return하거나

```js
function fn(person) {
	person.name = "lee";
}
var o1 = {name: "kim"}
var o2 = Object.assign({}, o1)
fn(o2); // 복제한 값을 넘김

console.log(o1); // {name: "kim"}
console.log(o2); // {name: "lee"}
```

복제한 값을 함수의 인자로 넘김으로써 불변 함수를 만들 수 있다.

### Object.freeze
그렇다면 객체를 수정하는 것을 원천적으로 막는 방법은 없을까? 누구도 수정하지 못하도록 객체 얼려버리면 된다! 원본을 바꾸지 못하게 하는 것이다.

> [Object.freeze()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)를 사용해서 객체를 얼려버리기 

```js
const o1 = {name: "kim", score: [1,2]}
Object.freeze(o1);
o1.name = "lee";
console.log(o1); // {name: "kim", score: [1,2]}/
```

### 함수형 프로그래밍 
함수형 프로그래밍에서는 부품이 언제나 똑같은 결과 값을 내놓아야 한다. 그렇게 하기 위해서는 함수가 순수한 상태를 유지해야 한다. `pure function`을 만들기 위해서는 함수로 들어가는 입력값 (객체)가 불변 객체여야 한다. 즉 `Immutability`를 이용해서 순수함수를 만들어 부품으로써 사용할 수 있다.

## Immutable.js
그렇다면 이번에는 [Immutable.js](https://immutable-js.github.io/immutable-js/) 라이브러리에 대해 알아보자. 이 라이브러리는 어떻게 생겨나게 된걸까?

페이스북에서 일하고 있는 Lee Byron은 graphQL과 이 라이브러리를 만드는 작업에 참여했다. 그의 이 라이브러리에 대한 강연은 [React.js Conf 2015 - Immutable Data and React - YouTube](https://www.youtube.com/watch?v=I7IdS-PbEgI)에서 확인할 수 있다.

우선 불변성을 유지하기란 까다롭고, 주의해야 할 것도 많다. 이러한 장벽을 낮추기 위한 라이브러리라고 볼 수 있다. 이 라이브러리에서는 데이터 타입으로 불변함을 강제해주고, 복제하면서 생기는 성능상의 악영향을 낮춰준다.

한마디로, 자바스크립트를 위한 immutable 컬렉션을 가진 라이브러리이다.
그가 강연에서 소개하는 예제 코드를 살펴보자.

```js
import { List } from "immutable";
var list = List.of(1, 2, 3); // List [1, 2, 3]
var list2 = list.push(4); List [1, 2, 3, 4]
list // List [1, 2, 3]
list2 === list // false
```

List와 push가 어떻게 작동하길래 push를 사용해서 원본의 불변성이 지켜지는걸까?

```js
List.prototype.push = function (value) {
  // 1) make a copy
	var clone = deepCopy(this);
	// 2) edit the copy
	clone[clone.length] = value;
	// 3) return the copy
	return clone;
}
```

절대 원본을 건들지 않고 무조건 복사본을 건드는 것을 확인할 수 있다.

### API
[Immutable.js](https://immutable-js.github.io/immutable-js/docs/#/) 문서에 나와있는 API 중 몇가지를 살펴보자.

-  [List](https://immutable-js.github.io/immutable-js/docs/#/List) 
	- 자바스크립트 배열과 같다
- [Map](https://immutable-js.github.io/immutable-js/docs/#/Map) 
	- `(key, value)`로 이루어진 키 기반의 컬렉션
- [Set](https://immutable-js.github.io/immutable-js/docs/#/Set) 
	- 설정할 때 사용하는 API
- [get()](https://immutable-js.github.io/immutable-js/docs/#/get) 
	- 제공된 키에 할당 된 값을 리턴한다
- [update()](https://immutable-js.github.io/immutable-js/docs/#/update) 
	- 기존 값을 업데이트 함수에 제공한 결과값을 리턴
- [remove()](https://immutable-js.github.io/immutable-js/docs/#/remove) 
	- 키의 값이 제거된 컬렉션의 복사본을 리턴한다
