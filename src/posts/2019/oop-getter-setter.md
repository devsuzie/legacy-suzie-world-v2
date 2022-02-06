---
title: OOP 그리고 getter/setter
description: getter setter을 쓰는 이유는 뭘까?
date: 2019-06-23
tags:
  - posts
layout: layouts/post.njk
---

"일반적으로 OOP 언어에서 getter/setter가 사용되는 이유는 OOP의 기본 개념 중 하나
인 캡슐화 (encapsulation) 때문입니다. 캡슐화라는 것은 간단히 말하면 오브젝트 내
부의 성질을 오브젝트 외부에서 직접 관여하지 못하게 하는 것이고 오직 오브젝트가
제공하는 루트를 통해서만 관여할 수 있게끔 하는 것입니다. 참고로 자바스크립트 전
문은 아닙니다만 위에 제시하신 get, set의 예제는 실용적인 측면에서 의미가 있을 수
는 있으나, prop.x 하지 않고도 그냥 x에 직접 접근이 가능하기 때문에 올바른 캡슐화
가 아닙니다. 오브젝트 외부에서는 x에 직접 접근은 커녕 오브젝트 내부에 실제로 x라
는 변수가 있는지 없는지 신경 쓸 필요도 없게 만드는 것이 캡슐화입니다."

위 문구는 OKKY의
[javascript getter setter 쓰는 이유](https://okky.kr/article/472722)에서 EF님의
답글을 긁어온것이다. 위 문구를 생각하며 getter/setter에 대한 예제를 살펴보자!

## getter

getter은 속성의 값을 얻어오는 메서드이다. 어떤 프로퍼티에 접근할 때마다 그 값을
계산하도록 해야 할 때 쓴다. 또 내부 변수의 상태를 명시적인 함수 호출 없이 보여주
고 싶을 때에도 쓴다.

> 출처:
> [MDN - Getter](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/get#%EC%84%A4%EB%AA%85)

```javascript
const human = {
  firstName: 'Suzie',
  lastName: 'Kim',
  get name() {
    return `${this.firstName} ${this.lastName}`;
  },
};

console.log(human.name); //Suzie Kim
```

getter을 쓰지 않고 위 코드를 적어보자.

```javascript
const human2 = {
  firstName: 'Suzie',
  lastName: 'Kim',
};

const name = `${human2.firstName} ${human2.lastName}`;

console.log(name); //Suzie Kim
```

getter을 사용하므로써 별도의 함수(위의 예시에서는 `name`)를 만들지 않아도 된다.
하지만 getter은 파라미터를 가질 수 없다는 점을 주의해야한다.

## setter

seter은 객체의 프로퍼티를 설정하는 함수이다. 프로퍼티 값이 변경되어질때마다 함수
를 실행하는데 사용된다. Setter은 한개의 파라미터만 가질 수 있다.

```javascript
const human = {
  firstName: 'Suzie',
  lastName: 'Kim',
  get name() {
    return `${this.firstName} ${this.lastName}`;
  },
  set name(name) {
    const names = name.trim().split(' ');
    this.firstName = names[0];
    this.lastName = names[1];
  },
};

human.name = '   Suzie Kim ';
console.log(human.firstName); //Suzie
console.log(human.lastName); //Kim
```

위 코드에서 setter은 `name`을 파라미터로 가진다. setter안에 정의된 `names`는 파
라미터 값을 받으면 공백 문자열을 기준으로 나누어 준다. human.name은 Setter을 통
해 `Suzie`와 `Kim`으로 나뉘게 된다.
