---
title: Rest Parameter 그리고 Spread Syntax
description: 자바스크립트에서 ... 점 세개는 무엇을 의미할까
date: 2019-07-22
tags:
  - posts
layout: layouts/post.njk
---

예전에 한 자바스크립트 코드에서 `...` 점 세개를 보고 당혹함을 감출 수 없었던 기억이 난다. 그건 마치 붙어있는 두개의 괄호 `()()`를 봤을때와 같은 기분이였다. 이번 글에서 드디어 그 점 세개의 정체에 대해서 말할 수 있게 되어 기쁘다. Rest Parameter 그리고 Spread Syntax, Destructuring에 대해 알아보자!

## The Rest Parameter
Rest 파라미터 구문은 정해지지 않은 수(an indefinite number, 부정수) 인수를 배열로 나타낼 수 있게 한다. 고 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/rest_parameters)에서 설명한다. 오~ 무슨 말이지? 아래 예시를 보자.

```javascript
const calculateAverage = (...numbers) => {
    let sum = 0
    numbers.forEach((num) => sum += num)
    return sum / numbers.length
}

console.log(calculateAverage(0, 100, 88, 64)) //63
```

sum은 0이다. numbers(parameter안에 들어있는 숫자들) 각각, 즉 num을 꺼내자. sum은 sum과 num을 더한 값이기 때문에 0 + 0 = 0, 0 + 100 = 100, 100 + 88 = 188, 188 + 64 = 252이 되어 sum의 값은 252가 된다. 252를 `numbers.length`인 4로 나누면 console에는 63이 나오게 된다. Rest 파라미터.. 뭐라 설명하기엔 어렵지만 언제 쓰는건지 알겠다!

string을 parameter에 추가하고싶으면 어떻게 할까?
```javascript
const calculateAverage = (thing, ...numbers) => {
    let sum = 0
    numbers.forEach((num) => sum += num)
    const average = sum / numbers.length
    return `The average ${thing} is ${average}`
}

console.log(calculateAverage('grade', 0, 100, 88, 64))
```
이런식으로 나머지 parameter빼고 추가할 수 있다. 

## The Spread Syntax
전개 구문을 사용하면 배열이나 문자열과 같이 반복 가능한 문자를 0개 이상의 인수 (함수로 호출할 경우) 또는 요소 (배열 리터럴의 경우)로 확장하여, 0개 이상의 키-값의 쌍으로 객체로 확장시킬 수 있습니다. 라고 역시 [MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)에서 말해주고 있다. 한마디로 말하자면 Rest parameter의 반대 기능인 것이다. 이번에도 예시를 통해 확인해보자!

```javascript
const printTeam = (teamName, coach, ...players) => {
    console.log(`Team: ${teamName}`)
    console.log(`Coach: ${coach}`)
    console.log(`Players: ${players.join(', ')}`)
}

const team = {
    name: 'Library',
    coach: 'Casey Penn', 
    players: ['Marge', 'Aiden', 'Herbert', 'Sherry']
}

PrintTeam(team.name, tea.coach, ...team.players)
```
전개 구문을 사용하면 이렇게 반대로 function을 호출할 수 있다.

```javascript
const cities = ['Barcelona', 'Cape Town', 'Bordeaux']
const citiesCoppy = [...cities]

console.log(cities) // Array ["Barcelona", "Cape Town", "Bordeaux"]
console.log(citiesCopy) // Array ["Barcelona", "Cape Town", "Bordeaux"]
```
`const citiesCopy = [cities]`하면 array안에 array가 생길것이다. 그럴때 spread syntax 쓰면 된다. 그러면 console에 같은 결과 나타난것을 볼 수 있을것이다.


## The Object Spread Syntax
전개 구문의 진정한 가치는 Object에 사용할때 드러난다. 침실, 화장실 수 그리고 건축년도 정보를 포함하는 `house`라는 이름의 객체를 정의했다고 가정해보자. `newHouse`라는 이름으로 기존의 `house`객체 안에 있던 정보를 그대로 가져오지만 건축년도 정보만 수정하고 싶을 때 유용하게 사용할 수 있다. 아래 예제를 보자.

```javascript
let house = {
    bedroom: 2,
    bathroom: 1.5,
    yearBuilt: 2017
}
let newHouse = {
    ...house
}
newHouse.yearBUilt = 2019

console.log(house) // Object { bedroom: 2, bathroom: 1.5, yearBuilt: 2017 }
console.log(newHouse) // Object { bedroom: 2, bathroom: 1.5, yearBuilt: 2019 }
```
이런식으로 활용 가능하다. Object를 합치거나 덮어쓸 때 유용하다. 정보를 덮어쓸 때에는 명시하는 순서가 중요하다. 마지막에 명시해야 정보를 덮어쓸 수 있다는 점을 유의하자.

## Destructuring
이또한 object랑 일할때 너무나 편리한 것이다. 기존에는 객체 안의 정보를 가져오고 싶을때에는 `object.method`의 형식을 사용했을 것이다.
```javascript
const todo = {
    id: 'sadkfljak',
    text: 'Pay the bills',
    completed: false
}

const text = todo.text
const completed = todo.completed

console.log(text) //Pay the bills
console.log(completed) //false
```

destructure 버전으로 위 코드를 작성해보자. 객체의 메서드 정보를 담을 새로운 변수를 정의할 필요없이 `const { method1, method2 } = object` 이 한줄로 실행 가능하다.
```javascript
const todo = {
    id: 'sadkfljak',
    text: 'Pay the bills',
    completed: false
}

const { text, completed } = todo

console.log(text) //Pay the bills
console.log(completed) //false
```

정보를 수정하거나 덮어쓰고 싶을때에는 아래의 코드처럼 콜론`:`을 사용해 코드를 작성해주면 된다.
```javascript
const todo = {
    id: 'sadkfljak',
    text: 'Pay the bills',
    completed: false
}

const { text:todoText, completed } = todo

console.log(todoText) //Pay the bills
console.log(completed) //false
```

정보를 추가하고 싶을때에는 등호 표시`=`를 사용해 추가하고 싶은 정보를 작성해주면 된다.
```javascript
const todo = {
    id: 'sadkfljak',
    text: 'Pay the bills',
    completed: false
}

const { text:todoText, completed, details = 'No details provided' } = todo

console.log(todoText) //Pay the bills
console.log(completed) //false
console.log(details) //No details provided
```

## Array
Object가 아닌 Array에서는 어떻게 쓸 수 있을까? 이름을 하나씩 명시할 필요 없이 쉼표`,`와 공백으로 배열의 순서를 표시할 수 있다. 
```javascript
const age = [65, 0, 13, 21]
const [firstAge, secondAge, , lastAge] = age

console.log(firstAge) // 65
console.log(lastAge) // 21
```

rest operator를 사용한 배열을 살펴보자. `...otherAges`로 명시한 배열의 내용물 말고 나머지를 표현할 수 있다.
```javascript
const age = [65, 0, 13]
const [firstAge, ...otherAges] = age

console.log(firstAge) // 65
console.log(otherAges) // Array [ 0 , 13 ]
```

## Function argument
함수에서는 argument의 이름을 지정할 필요 없이 사용하고 싶은 메서드를 바로 명시함으로써 사용할 수 있다. 
```javascript
const printTodo = (todo) => {
    console.log(`${todo.text}: ${todo.completed}`)
}
printTodo(todo)
```

위의 코드는 아래의 코드처럼 바꾸어 쓸 수 있다.
```javascript
const printTodo = ({ text, completed }) => {
    console.log(`${text}: ${completed}`)
}
printTodo(todo)
```