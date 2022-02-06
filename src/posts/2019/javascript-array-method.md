---
title: JavaScript Array Method
description: 수많은 method를 찾아서..
date: 2019-06-04
tags:
  - posts
layout: layouts/post.njk
---

Javascript 배열에는 무수히 많은 method가 있다. 그중에서 헷갈리는 몇가지를 정리해보았다. 
배열을 검색해 찾을 수 있는 indexOf, findIndex, find와 filtering arrays, sorting arrays에 대해 알아볼 예정이다. 

## Searching Arrays
### indexOf
indexOf는 찾고자 하는 것과 정확히 일치(===)하는 첫번째 요소의 인덱스를 반환한다.
즉 indexOf method는 object를 찾을 때는 사용할 수 없다. {} === {} 는 false이기 때문이다.
일치하는 것을 찾지 못하면 -1을 반환한다.

```javascript
const places = ['Seoul', 'SanJose', 'Cebu']
const index = places.indexOf('SanJose')

console.log(index) // 1
```

### findIndex
indexOf와 마찬가지로 일치하는 것을 찾지 못했을 때 -1을 반환한다. 
일치하는 것을 찾으면 callback function에서 true를 반환한다.
보조 함수를 써서 검색 조건을 저장할 수 있으므로 indexOf보다 더 다양한 상황에서 활용 가능하다.
findIndex는 검색을 시작할 인덱스를 지정할 수 없다.

```javascript
const notes = [{
    title: 'My next trip',
    body: 'I would like to go to Spain'
}, {
    title: 'Habbits to work on',
    body: 'Exercise. Eating a bit better'
}, {
    title: 'Office modification',
    body: 'Get a new seat'
}]

const index = notes.findIndex(function (note, index) {
    return note.title === 'Habbits to work on'
})

console.log(index) // 1
```

> index에 정의된 함수는 아래처럼 Arrow function으로 쓸 수 있다.

```javascript
const index = notes.findIndex((note, index) => note.title === 'Habbits to work on')
```

### find
조건에 맞는 요소의 인덱스가 아닌 요소 자체를 원할 때 사용하는 method
findIndex와 마찬가지로 검색 조건을 함수로 전달 가능.
조건에 맞는 요소가 없을 때는 undefined를 반환.

findIndex와 비슷하지만 인덱스가 아닌 요소 자체를 반환한다는게 다른점이다.

```javascript
const notes = [{
    title: 'My next trip',
    body: 'I would like to go to Spain'
}, {
    title: 'Habbits to work on',
    body: 'Exercise. Eating a bit better'
}, {
    title: 'Office modification',
    body: 'Get a new seat'
}]

const findNote = function (notes, noteTitle) {
    return notes.find(function (note, index) {
        return note.title.toLowerCase() === noteTitle.toLowerCase()
    })
}

const note = findNote(notes, 'my next trip')
console.log(note) // { title: 'My next trip', body: 'I would like to go to Spain' }
```

> toLowerCase()는 사용자가 검색값을 입력할때 대/소문자 구별을 하지 않아도 되게 해준다.

## Filtering Arrays
### filter
말그대로 배열을 필터링하게 해준다.
사본을 반환하며 새 배열에는 필요한 요소만 남는다.
새 배열에 item을 포함시키고 싶다면 callback function은 true를 반환할것이고,
item을 제외시키고 싶다면 false를 반환한다.

```javascript
const todos = [{
    text: 'Order cat food',
    completed: false
},{
    text: 'Clean kitchen',
    completed: true
},{
    text: 'Do work',
    completed: false
}]

const getThingsToDo = function (todos) {
    return todos.filter(function (todo) {
        return !todo.completed
    })
}

// 해야할 일의 목록
console.log(getThingsToDo(todos))
```

## Sorting Arrays
### sort
sort는 원하는대로 정렬할 수 있게 해준다.
sort method 또한 callback function을 가지고 있는데 다른 array method들과 다르게 개개의 요소를 불러오는게 아니라, 두개의 요소 (대개 a와 b)로 부터 불러온다.
두개의 요소중 어떤것이 먼저 오게 할지 정할 수 있다.
a가 먼저 오게 되면 -1이 반환되고, b가 먼저 오게 되면 1이 반환된다. 만약 a와 b가 같다면 0이 반환된다.

```javascript
const todos = [{
    text: 'Order cat food',
    completed: false
},{
    text: 'Clean kitchen',
    completed: true
},{
    text: 'Do work',
    completed: false
}]

const sortTodos = function (todos) {
    todos.sort(function (a, b) {
        if (!a.completed && b.completed) {
            return -1
        } else if (!b.completed && a.completed) {
            return 1
        } else {
            return 0
        }
    })
}

sortTodos(todos)
console.log(todos) // 해야할것이 한것보다 먼저 정렬된다.
```