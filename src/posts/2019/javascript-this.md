---
title: 이거는 this
description: this는 자바스크립트에서 어떻게 작동하는가
date: 2019-11-18
tags:
  - posts
layout: layouts/post.njk
------

이번 글에서는 몇달전부터 쓰고 싶어했던 `this`에 대해서 이야기해볼 예정이다.
이해했다고 생각하면 멀어지고, 이제는 정말 이해했다고 생각하면 멀어지는 존재... `this`.
내 기준으로 JavaScript 자습서(?)에는 3대장이 있다. 1.You don't know JS 2.코뿔소 Leraning Javascript 3.MDN이 바로 대장님들이다.
대장님들이 말하는 `this`에 대해서 보고 또 보니까 대충은 감이 온다. 그래서 용기내어 적어보는 글이다.
this는 함수를 어떻게 선언했느냐가 아니라 어떻게 호출했느냐에 따라 달라진다는 점을 기억하며 본문을 읽어보자.

## new 키워드
new 키워드를 사용하면 새로운 빈 객체를 생성한다. constructor function은 새로운 객체를 가리킨다.
아래 코드에서의 this는 새로운 객체에 묶이게 된다. 
```javascript
function foo(name) {
    this.name = name
    console.log(this) // foo { name: 'suzie' }
}

const me = new foo('suzie')
```

## Explicit Binding
this값이 되고자 하는것을 정확하게 가리키고 있다면 그것이 Explicit Binding이다.
call, apply, bind가 함수의 호출과 생성에 사용되는 경우를 말한다. 이 때 함수 내의 this는 인수로 전달된 객체이다.
차례대로 하나씩 살펴보자!

### call
> call 메서드는 주어진 `this`값 및 각각 전달된 인수와 함께 함수를 호출합니다. [(출처: mdn)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

```javascript
const me = { name: 'Suzie' }

function greet() {
    return `Hello, I'm ${this.name}!`
}

console.log(greet()) // Hello, I'm undefined!
console.log(greet.call(me)) // Hello, I'm Suzie!
```
greet() 함수는 어떤 객체에도 연결되지 않았기 때문에 이 함수의 this는 어디에도 묶이지 않는다.
그렇다면 call 메서드를 사용해 `me` 변수에 묶어보자.
call을 사용하므로써 해당 함수인 `greet()`이 `me` 객체의 메서드인것 처럼 사용할 수 있게 되었다.

이번에는 첫번째 매개변수인`this`로 사용할 값과 함께 인수목록 또한 매개변수로 주어졌을때의 경우를 알아보자.
```javascript
const me = { name: 'Suzie' }

function update(birthYear, occupation) {
    this.birthYear = birthYear
    this.occupation = occupation
}

update.call(me, 1997, 'programmer')

console.log(me) // { name: 'Suzie', birthYear: 1997, occupation: 'programmer' }
```
첫번째 매개변수인 `me`는 this로 사용되어 update() 함수가 `me`의 메서드인것 처럼 사용할 수 있게 되었다.
두번째와 세번째 매개변수인 탄생연도 정보와 직업정보는 인수 목록이다. 이 인수 목록은 호출하는 함수로 전달된다.
위의 예시에서는 `me`객체의 함수인 `update()`로 전달된것을 볼 수 있다.

### apply
> apply() 메서드는 주어진 this 값과 배열 (또는 유사 배열 객체) 로 제공되는 arguments 로 함수를 호출합니다. [(출처: mdn)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

apply는 call과 매우 비슷하다. call은 일반적인 함수처럼 매개변수를 직접 받지만, apply는 매개변수를 `배열`로 받는다.
call에서 사용한 동일한 코드를 apply 메서드를 이용해 다시 작성해보자.
```javascript
const me = { name: 'Suzie' }

function update(birthYear, occupation) {
    this.birthYear = birthYear
    this.occupation = occupation
}

update.apply(me, [1997, 'programmer'])

console.log(me) // { name: 'Suzie', birthYear: 1997, occupation: 'programmer' }
```
동일한 결과가 콘솔에 찍히는것을 볼 수 있다.

apply메서드를 이용하면 배열의 최솟값과 최댓값을 쉽게 구할 수 있다. 아래 코드를 보자.
```javascript
const arr = [2, 3, -5, 15, 7]
Math.min.apply(null, arr) // -5
Math.max.apply(null, arr) // 15
```
첫번째 매개변수에 this의 값이 아닌 null을 쓴 이유는 Math.min과 Math.max가 this와 관계없이 동작하기 때문이다.


### bind
> bind() 메서드는 새로운 함수를 생성합니다. bind()가 호출된 함수의 this 키워드를 주어진 값으로 설정하고, 새로운 함수의 인수(argument) 앞에 지정한 인수 시퀀스가 사용됩니다. [(출처: mdn)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)

bind를 사용하면 this의 값을 영구히 바꿀 수 있다.
this의 값이 항상 `me`가 되도록 하려면 bind를 사용한다.
```javascript
const me = { name: 'Suzie' }

function update(birthYear, occupation) {
    this.birthYear = birthYear
    this.occupation = occupation
}

const updateMe = update.bind(me)
updateMe(2019, 'rich')

console.log(me) // { name: 'Suzie', birthYear: 2019, occupation: 'rich' }
```
bind를 사용한 함수는 call이나 apply, 다른 bind와 함께 사용할 수 없다.
bind가 함수의 동작을 영구적으로 바꾸기 때문이다.
그렇기 때문에 bind를 사용할 때에는 함수의 this가 어디에 묶이는지 정확히 파악하고 사용해야한다.

mdn에서는 bind 메서드가 새로운 함수를 생성한다고 설명했다.
어떻게 새로운 함수를 생성하는걸까?
위의 코드를 보면 매개변수를 받으면서 호출되는 새 함수인 `updateMe()`를 만든것을 볼 수있다.
그렇기 때문에 bind메서드로 매개변수인 년도와 직업을 고정했지만 바꿀 수 있는 업데이트 함수를 만들고 싶다면 아래처럼 하면 된다.

```javascript
const me = { name: 'Suzie' }

function update(birthYear, occupation) {
    this.birthYear = birthYear
    this.occupation = occupation
}

const updateMe = update.bind(me)
updateMe(2019, 'rich') // { name: 'Suzie', birthYear: 2019, occupation: 'rich' }

const updateMe2020 = update.bind(me)
updateMe2020(2020, 'bugatti owner') // { name: 'Suzie', birthYear: 2020, occupation: 'bugatti owner' }
console.log(me)
```
생성한 updateMe2020 함수를 호출하자 값이 변경된것을 볼 수 있다.

## Implicit binding
`obj.method()`와 같이 함수를 메서드로 호출하는 경우, this는 함수가 프로터피인 객체이다.
```javascript
const foo = {
    name: 'suzie',
    me() {
        console.log(this)
    }
}

foo.me() // { name: 'suzie', play: [Function: play] } 
```
`me()`는 foo 객체의 메서드이기 때문에 this는 foo 객체를 가리킨다.
메서드를 호출하면 this는 호출한 메서드를 소유하는 객체가 된다.

## Default Binding
```javascript
function foo() {
    console.log(this)
}

foo()
```
브라우저에서는 winodw, node 환경에서는 global이다.
엄격모드 `use strict`에서는 undefined이다.

## this is undefined 문제 해결하기
우리는 가끔씩 this를 참조할 때 undefined로 표시되는 문제를 겪고는 한다.
그 때 바로 this가 한걸음 멀어지는 것을 느낄 수 있는데.. 이러한 문제를 해결해보자. 
우선 아래의 코드를 보자.
```javascript
const me = {
    name: 'suzie',
    favoriteFoods: ['chicken', 'hamburger', 'pizza'],
    showFav() {
        this.favoriteFoods.forEach(function(favoriteFood) {
            console.log(favoriteFood) // chicken hamburger pizza
        })
    }
}

me.showFav()
```
좋아하는 음식이 순서대로 콘솔에 찍히는것을 볼 수 있다.
이번에는 좋아하는 음식 앞에 이름을 붙여서 호출해보자.

```javascript
const me = {
    name: 'suzie',
    favoriteFoods: ['chicken', 'hamburger', 'pizza'],
    showFav() {
        this.favoriteFoods.forEach(function(favoriteFood) {
            console.log(this.name, favoriteFood)
        })
    }
}

me.showFav()
```
이렇게 하면 되겠지? 하고 콘솔창을 확인하니 undefined만이 이름의 자리를 대신하고 있다.
어떻게 된 일일까.
위의 `console.log`안에 있는 this는 `me`객체가 아닌 전역 객체를 가리키고 있다. (브라우저에서는 window, node에서는 gloabl)
`me`객체 안에 있으니까 `me`객체를 가리켜야하는것 아닌가? 라고 생각 할 수 있겠다.
하지만 forEach의 callback function으로 쓰여지고 있는 이상 이 function은 그냥 regular function일 뿐이다.
때문에 global 을 가리키고 있는 것이다.
me 객체 안에서 메서드로써 호출되고 있는 것은 `showFav()` 뿐이다.
그러면 어떻게 이 문제를 해결할 수 있을까?

### 해결 방법1. thisArg 파라미터로 this값 정의하기
```javascript
const me = {
    name: 'suzie',
    favoriteFoods: ['chicken', 'hamburger', 'pizza'],
    showFav() {
        this.favoriteFoods.forEach(function(favoriteFood) {
            console.log(this.name, favoriteFood) 
        }, { name: 'suzie' })
    }
}

me.showFav()
```
forEach 메서드는 두가지 파라미터를 가진다.
하나는 callback function이고, 나머지 하나는 `thisArg`이다.
이곳에 객체를 패스하면 `this`는 이곳에 패스된 객체를 가리킬 것이다.
이제 favortieFoods 요소와 이름이 함께 출력되는 것을 확인할 수 있다.

{ name: 'suzie' } 대신 `this`를 패스해도 똑같이 작동한다.
이 시점에서는 이미 callback function을 벗어났기 때문에 여기서 호출하는 this는 `me`객체를 가리키기 때문이다.


### 해결 방법 2. bind로 this의 값 정의하기
bind를 사용할 경우 함수의 this값을 영구히 바꾸게 된다.
아래의 코드에서는 this값을 `me`로 정의하므로써 showFav()의 this, callback function 내부의 this 모두 `me`를 가리키게 된다.
```javascript
const me = {
    name: 'suzie',
    favoriteFoods: ['chicken', 'hamburger', 'pizza'],
    showFav() {
        this.favoriteFoods.forEach(function(favoriteFood) {
            console.log(this.name, favoriteFood)
        }.bind(me))
    }
}

me.showFav()
```

### 해결 방법 3. arrow function
함수가 화살표 함수인 경우에는 생성된 시점에서 주변 스코프의 this 값을 받는다.
그렇게 되면 `showFav()`의 call function 내부의 this는 주변 스코프인 `this.favoriteFoods`의 this 값을 받게 된다.
그래서 callback function 안의 this 값은 `me`를 가리키게 되어 정상적으로 작동하는 모습을 볼 수 있다.
```javascript
const me = {
    name: 'suzie',
    favoriteFoods: ['chicken', 'hamburger', 'pizza'],
    showFav() {
        this.favoriteFoods.forEach((favoriteFood) => {
            console.log(this.name, favoriteFood)
        })
    }
}

me.showFav()
```
> 화살표 함수는 스스로의 this 바인딩을 제공하지 않는다.


## 마무리
자바스크립트를 처음 접하기 시작하면서부터 혼란스러워 했던 개념인 `this`를 나름대로 정리해봤다. 
처음에 `this`를 사용하면서 느꼈던 좌절감이 떠오른다.. 그때는 `this` 때문에 힘들었다면 지금은 제네레이터와 이터레이터때문에 힘들다. 다음 글에서는 나를 힘들게 하는 제네레이터와 이터레이터에 대해서 얘기해볼 예정이다.. 기대해주세요!