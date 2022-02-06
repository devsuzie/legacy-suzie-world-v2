---
title: Asynchronous Programming
description: Callback, Promises 그리고 Async-await
date: 2019-08-21
tags:
  - posts
layout: layouts/post.njk
---

자바스크립트는 단일 스레드에서 동작한다. 콜스택이 하나이기 때문에 한 번에 한 가지 일만 할 수 있다는 뜻이다. 문제는 바로 여기서 발생된다. 만약 콜스택 내에 수행 시간이 긴 함수가 있다면 어떻게 될까? `blocking`되어 브라우저가 동작하지 않는것 처럼 보이는 현상이 발생할것이다. 그렇기 때문에 자바스크립트 프로그래밍을 할 때에는 문제를 비동기적 관점에서 생각하는것이 중요하다. 그렇다면 어떻게 비동기적 프로그래밍을 할 수 있을까? `callback`, `promise`, `async/await`를 사용해 할 수 있다. 하나씩 차례대로 알아보자. 

> 세가지 비동기적 메커니즘을 살펴볼때 모두 같은 예제를 사용한다. 아래에 나오는 예제는 [이 영상](https://youtu.be/PoRJizFvM7s)에서 참고했다. 

## call back
콜백을 살펴보기전에, 두개의 객체를 포함하는 `posts`배열을 만들고 `createPosts`함수로 새로운 객체를 배열에 push하는 코드를 작성해보자. (HTML 파일이 있다는 가정하에 작성한 코드이다)

```javascript
const posts = [
    { title: 'Post One', body: 'This is post one' },
    { title: 'Post Two', body: 'This is post two' },
];

function getPosts() {
    setTimeout(() => {
        let output = '';
        posts.forEach((post, index) => {
            output += `<li>${post.title}</li>`;
        });
        document.body.innerHTML = output;
    }, 1000);
}

function createPost(post) {
    setTimeout(() => {
        posts.push(post);
    }, 2000) 
}

getPosts();

createPost({ title: 'Post Three', body: 'This is post three' })
```
위 코드를 실행하면 'Post Three'는 랜더링 되지 않았음을 알 수 있다.
화면에 결과를 나타내는 함수 `getPosts`는 1초 후에 실행되고, 'Post Three'를 push 하는 함수는 2초뒤에 실행되기 때문에 이미 DOM에 결과가 나타난 이후에는 push 해봤자 소용없는것이다. 
바로 이때 callback이 필요하게 된다.

```javascript
const posts = [
    { title: 'Post One', body: 'This is post one' },
    { title: 'Post Two', body: 'This is post two' },
];

function getPosts() {
    setTimeout(() => {
        let output = '';
        posts.forEach((post, index) => {
            output += `<li>${post.title}</li>`;
        });
        document.body.innerHTML = output;
    }, 1000);
}

function createPost(post, callback) {
    setTimeout(() => {
        posts.push(post);
        callback();
    }, 2000); 
}

createPost({ title: 'Post Three', body: 'This is post three' }, getPosts)
```
다시 코드를 실행하면 2초 후 Post One, Two, Three가 동시에 화면에 나타나는 모습을 볼 수 있다. 어떻게 된 것일까? 위의 코드를 보면 `createPost`함수의 파라미터 값으로 `callback`이 추가된것을 볼 수 있다. (이름은 무엇으로 하든 상관 없다. 주로 `callback` 또는 `cb`으로 작성한다.) post를 push한 이후에 바로 callback을 호출한다. 기존의 코드는 getPost함수와 createPost함수를 각각 호출하는 방식이였다면 위 코드에서는 `getPosts`함수가 createPost함수의 인자로써 callback으로 전달된 모습을 볼 수 있다.

getPosts 함수가 createPost 함수보다 먼저 선언되었지만 나중에 호출함으로써 비동기 실행이 가능하게 되었다.
이것이 `callback`이다. callback은 나중에 호출할 함수이다. 보통 익명함수로 쓰인다. 이제 콜백이 무엇인지도 알았고, 어떻게 사용하는것인지도 알았으니 모든 문제가 해결될 것 같지만 아니다. 한번에 여러가지를 기다려야하거나 추가적으로 호출해야할 경우가 생긴다면 콜백을 관리하기가 상당히 어려워진다. 그러면 중괄호로 둘러싸여 끝없이 중첩된 삼각형의 코드 블록이 생기게 되는데, 이를 `콜백헬`이라고 부른다. 

이 `콜벡헬`을 해결하려는 시도 속에서 만들어진것이 `프라미스`이다.

## promise
프라미스는 콜백을 예측 가능한 패턴으로 사용할 수 있게 한다. 프라미스 기반 비동기적 함수를 호출하면 그 함수는 promise 인스턴스를 반환한다. 프라미스의 상태값에는 세가지가 있다. 첫번째는 `대기(Pending)`, 비동기 처리 로직이 완료 되지 않은 상태이다. 두번째는 `이행(Fullfilled)`, 비동기 처리 로직이 완료된 상태이다. 프로미스가 결과값을 반환한다. 세번째는 `실패(Rejected)`로 비동기 처리가 실패했거나 오류가 발생한 상태이다. 즉 프라미스는 성공하거나 실패하거나 둘 중 하나만 일어난다. 

또 프라미스는 객체이므로 어디든 전달할 수 있다. 비동기적 처리를 다른 함수에서 처리하게 하고 싶다면 프라미스를 넘기기만 하면 된다. 그렇다면 프라미스는 어떻게 만들 수 있을까? `성공(resolve)`과 `실패(reject)` 콜백이 있는 함수로 새 Promise 인스턴스를 만들기만 하면된다. 

callback에서 살펴본 예제를 프라미스를 사용하도록 고쳐보자. 

```javascript
const posts = [
    { title: 'Post One', body: 'This is post one' },
    { title: 'Post Two', body: 'This is post two' },
];

function getPosts() {
    setTimeout(() => {
        let output = '';
        posts.forEach((post, index) => {
            output += `<li>${post.title}</li>`;
        });
        document.body.innerHTML = output;
    }, 1000);
}

function createPost(post) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            posts.push(post);

            const error = false;

            if(!error) {
                resolve();
            } else {
                reject('Error: Something went wrong')
            }
        }, 2000); 
    });
}

createPost({ title: 'Post Three', body: 'This is post three' }).then(getPosts).catch(err => console.log(err));
```
`createPost`함수에 새 promise 인스턴스를 만들고 반환하게 한다. 매개변수(resolve, reject)를 받게 만들고 에러가 없으면 resolve를, 에러가 있으면 reject를 호출한다. 프라미스를 이용해 함수를 만들었다면 가장 아래 라인의 함수를 호출하는 부분을 살펴보자. `then`핸들러를 바로 호출해 성공 콜백을 받는다. 이어지는 `catch`핸들러는 에러 콜백을 받는다.

### promise.all()
promise를 많이 사용하게 되면 then 핸들러를 계속해서 이어써야한다. then 핸들러가 계속 이어져있는 코드는 그리 보기 좋은 코드는 아닐것이다. 이때 `promise.all()`을 쓴다. 아래의 코드는 [MDN - Promise.all()사용하기](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all#Promise.all_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)를 참고했다.

```javascript
var p1 = Promise.resolve(3);
var p2 = 1337;
var p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo");
  }, 100);
}); 

Promise.all([p1, p2, p3]).then(values => { 
  console.log(values); // [3, 1337, "foo"] 
});
```
이처럼 Promise.all()메서드는 배열 내 모든 값의 이행 (또는 첫번째 거부)를 기다린다. 위 코드에서는 `[p1, p2, p3]` 배열 내에 있는 모든 값이 이행되어 콘솔창에 나타나는 것을 볼 수 있다.

## async-await
promise를 사용해 비동기적 프로그래밍을 했을때 몇가지 문제가 있다. 첫번째로, 동일한 이름의 메서드인 `then()`을 연쇄적으로 호출하고 있어 몇번째 `then()`에서 문제가 발생했는지 발견하기 어려운 디버깅의 문제. 두번째로 동기 프로그래밍의 try/catch가 아닌 `catch()`로 예외 처리를 하고 있기 때문에 구분하기가 난해해진다. 세번째로 코드의 가독성 문제이다. 이런점들을 해결하기 위해 ES7에서 추가된것이 바로 `async-await` 키워드이다. 

async-await는 promise를 조금 더 우아하게 사용할 수 있게할 뿐만 아니라, 비동기 프로그래밍을 하면서 동기 코드처럼 보이게 한다. 예제를 살펴보자.  

```javascript
const posts = [
    { title: 'Post One', body: 'This is post one' },
    { title: 'Post Two', body: 'This is post two' },
];

function getPosts() {
    setTimeout(() => {
        let output = '';
        posts.forEach((post, index) => {
            output += `<li>${post.title}</li>`;
        });
        document.body.innerHTML = output;
    }, 1000);
}

function createPost(post) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            posts.push(post);

            const error = false;

            if(!error) {
                resolve();
            } else {
                reject('Error: Something went wrong')
            }
        }, 2000); 
    });
}

async function init() {
    await createPost({ title: 'Post three', body: 'This is post three' })

    getPosts();
}

init();
```
함수앞에 `async`를 붙여주고 비동기 처리되는 곳에 `await`를 추가했다. async 함수 자체는 암시적으로 promise를 반환한다. async 함수를 살펴보면 비동기 처리를 할 `createPost`함수 앞에 await가 추가된다. createPost 함수가 결과 값을 얻기 전까지 getPosts함수는 실행되지 않는다. 그리고 나서 async 함수를 호출하면 정상적으로 Post One, Two, Three가 화면에 나타나는 것을 볼 수 있다. 분명 비동기 처리를 하지만 읽을 때 동기 처리를 하는 것 처럼 읽을 수 있다. 

## 마무리
비동기적 프로그래밍은 자바스크립트로 더욱 복잡한 소프트웨어를 만들기 위해 필수 불가결한 것이다. 내가 사랑하는 코뿔소 책에서는 콜백과 프라미스를 훌륭하게 비유한 표현이 나와 있다. `콜백`은 식당에서 손님이 줄 서서 기다리지 않도록 전화번호를 받아 자리가 나면 전화를 해주는 것과 비슷하다. 전화번호(자리가 나면 알 수 있도록 하는 수단)를 손님이 식당에 넘긴다. `프라미스`는 자리가 났을 때 진동하는 호출기를 식당에서 손님에게 넘겨주는 것과 비슷하다. 이 비유를 잘 생각하며 콜백과 비동기적 프로그래밍에 대해 공부하니 더 이해하기 쉬웠다. 이번 글에서는 callback, promises, async/await에 대해 알아보았다면 비동기적 프로그래밍의 또 다른 패러다임인 `제네레이터`에 대해서는 다음에 알아볼 예정이다. 기대해주세요!