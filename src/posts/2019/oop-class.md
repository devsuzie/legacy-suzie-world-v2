---
title: Object Oriented Programming
description: Class를 사용한 객체 지향 프로그래밍
date: 2019-06-17
tags:
  - posts
layout: layouts/post.njk
---

코드 재사용이 용이하게 만드는것이 객체 지향 프로그래밍의 큰 특징이다. constructor function이라는 말이 자주 나왔는데, javascript에서 변수를 정의할때 쓰이는 `const`랑 같은것인줄 알고 헷갈렸었다. 알고보니 const는 constant의 축약이였다. 

> constructor: 제작자, 건설하다, 건축자, 생성자
> constant: 정수, 항수, 변함없는, 흔들리지 않는

constructor메서드는 class내에서 객체를 생성하고 초기화하기 위한 특별한 메서드이다. 클래스는 constructor라는 이름을 가진 특별한 메서드를 하나씩만 가질 수 있다. 두 개 이상의 constructor 메서드는 에러를 불러일으킨다. 생성자 메서드를 지정하지 않은 경우엔 기본 생성자 메서드를 사용한다.

> 출처: [Object.prototype.constructor](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor), [constructor](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/constructor)

```javascript
const Person = function (firsName, lastNae, age, likes = []) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.likes = likes
}

Person.prototype.getBio() = function () {
    let bio = `${this.firstName} is ${this.age}.`

    this.likes.forEach((like) => {
        bio += ` ${tihs.firstName} likes ${like}.`
    })

    return bio
}

Person.prototype.setName = function (fullName) {
    const names = fullName.split(' ')
    this.firstName = names[0]
    this.lastName = names[1]
}

const me = new Person('Suzie', 'Kim', 23, ['Coding', 'Read a Book'])
me.setName('Dale Carnegie')
console.log(me.getBio()) // 
```

## 하나씩 살펴보자
```javascript
const Person = function (firsName, lastNae, age, likes = []) {
    this.firstName = firstName
    this.lastName = lastName
    this.age = age
    this.likes = likes
}
```

여기서 `this.firstName = firstName`가 이해가 안간다면 아래의 예시를 참고하자. 아래 코드에서 this는 `Person{}`을 의미한다. 그래서 this.firstName을 'Suzie'로 지정하고 나서 new 생성자로 호출하면 { firstName: 'Suzie' }가 되는것이다.

```javascript
const Person = function () {
    cnosle.log(this) // Person {}
    this.firstName = 'Suzie' 
}

const me = new Person()
console.log(me) // Person { firstName: 'Suzie' }
```

##두번째로는
```javascript
Person.prototype.getBio() = function () {
    let bio = `${this.firstName} is ${this.age}.`

    this.likes.forEach((like) => {
        bio += ` ${tihs.firstName} likes ${like}.`
    })

    return bio
}
```
constructor 함수는 this binding을 위해 arrow function이 아닌 regular function을 쓰는데 안에 있는 `this.likes.forEach`함수는 arrow fucntion을 쓰고 있다. 심지어 이 arrow function 을 regular function 으로 바꾸면 오류가 나기까지 한다! 왜 그럴까? 그 이유는 `this binding`에 있다.
regular function인 constructor function은 `this.firstName`, `this.age`를 잘 참조하고 있다. 이것을 그대로 사용하기 위해서는 this binding을 하지 않는 arrow function을 사용해야 그대로 받아서 쓸 수 있기 때문에 화살표 함수를 사용한 것이다.

> 위 코드에서 `+=` 등호를 볼 수 있을것이다. `a = a +b`는 `a += b`로 표현될 수 있다.

## 세번째로는
```javascript
Person.prototype.setName = function (fullName) {
    const names = fullName.split(' ')
    this.firstName = names[0]
    this.lastName = names[1]
}
```

우선 위 코드에 나오는 split method에 대해서 알아보자. split은 문자에서 알 수 있듯이 말 그대로 분리해주는 역할을 한다. 예를들어 `'123456'.split('')` 이라고 하면 '1', '2', '3', '4', '5', '6' 이 된다.
위의 코드에서는 fullname을 스페이스바를 기준으로 나누었기 때문에 첫번째 [0]는 fisrtname이, 두번째는 [1] lastname이 된다.

## Class
class syntax를 사용하면 constructor 그리고 method를 쓰기 더 쉬워진다. 
> 참고: [JavaScript Factory Functions vs Constructor Function vs Classes](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e)
```javascript
class PersonClass {
    constructor(firstName, lastName, age, likes = []) {
        this.fisrtName = firstName
        this.lastName = lastName
        this.age = age
        this.likes = likes
    }
}

const myPerson = new PersonClass('Suzie', 'Kim', 22, ['Coding'])
console.log(myPerson) 
// PersonClass {
//     firstName: 'Suzie',
//     lastName: 'Kim',
//     age: 22,
//     likes: [ 'Coding' ]
// }
```

class syntax안에 method를 사용해보자. method를 정의할때는 class를 정의한 중괄호 안에 이어서 `쉼표 없이` 이어서 입력하면 된다.
```javascript
class PersonClass {
    constructor(firstName, lastName, age, likes = []) {
        this.fisrtName = firstName
        this.lastName = lastName
        this.age = age
        this.likes = likes
    }
    getBio() {
        let bio = `${this.firstName} is ${this.age}.`

        this.likes.forEach((like) => {
            bio += ` ${tihs.firstName} likes ${like}.`
        })

        return bio
    }
}

const myPerson = new PersonClass('Suzie', 'Kim', 22, ['Coding'])
console.log(myPerson.getBio()) //Suzie is 23. Suzie likes Coding.
```

> .prototype.methodname 으로 쓰던게 그냥 class definition 안에서 methodname() {}으로 끝나버린다. 업데이트하기에도, 읽기에도 훨씬 편하다. 

## sub classes
위의 class 코드와 이어지는 코드다.
```javascript
class Employee extends Person {
    constructor(firstName, lastName, age, position, likes) {
        super(firstName, lastName, age, likes) //parent로부터 불러오는 정보 
        this.position = position
    }
}

const me = new Employee('Suzie', 'Kim', 22, 'Developer', ['Coding'])
console.log(me)
// Employee {
//     firtsName: 'Suzie',
//     lastName: 'Kim',
//     age: 22,
//     likes: ['Coding'],
//     position: 'Developer'
// }
```
sub class는 parent class 앞에 extends를 사용하므로써 쓸 수 있다. sub class에도 constructor function을 설정한다. parent constructor function(Person)에 접근하기 위해서는 `super`argument를 불러야한다. super을 통해 설정되지 않은 subclass constructor 함수의 인자는 this를 통해 다시 설정해줘야한다. 아래 예시를 보자.

```javascript
class Student extends Person {
    constructor(firstName, lastName, age, grade, likes) {
        super(firstName, lastName, age, likes) //parent로부터 불러오는 정보 
        this.grade = grade
    }
    updateGrade(change) {
        this.grade += change
    }
}

const me = new Student('Suzie', 'Kim', 22, 88, ['Coding'])
me.updateGrade(+12)
console.log(me)
// Student {
//     firtsName: 'Suzie',
//     lastName: 'Kim',
//     age: 22,
//     likes: ['Coding'],
//     grade: 100  
// }
```
기존 Person의 정보에서 추가하려는것은 `grade`정보이다. super을 통해 부모로 부터 정보를 불러오고, 부모에 없던 새로 추가할 정보는 `this`를 사용해 설정했다. 그리고 메서드를 붙여주면 간단하게 원하는 결과를 얻을 수 있다. 나한테 100점 주고싶어서 `updateGrade(+12)`로 100점을 만들어줬다. 하하하. 가상으로라도 100점 받아봅시다 거참.

## 마무리
Class를 이용해 코딩을하면 우리의 삶이 한층 더 즐거워질뿐만 아니라 말 그대로 코드의 재사용이 용이해진다. ES6는 참 아름답다.. 사실 Class는 비교적 이해하기 쉬웠는데 this는 아직까지 자유롭게 쓰기에는 이해하지 못한 부분들이 있다. 다음에는 this에 대한 글을 써볼까 한다. 기대해주세요! 