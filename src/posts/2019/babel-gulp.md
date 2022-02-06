---
title: babel을 gulp와 함께 사용하기
description: 바벨과 걸프를 함께 사용해 ES6 코드를 ES5 코드로 바꿔보자!
date: 2019-05-04
tags:
  - posts
layout: layouts/post.njk
---

ES5가 웹 표준이지만 강력하고 사용하기 즐거운 언어인 ES6로 코드를 작성하기 위해서는 트랜스컴파일러가 필요하다. 사실 나는 ES6를 발행한 2015년 6월이 훨씬 지난 이후에 자바스크립트를 공부하기 시작했기 때문에 ES5 보다는 ES6로 코드를 작성하는데 친숙한 편이다. 사용자의 다양한 브라우저 환경을 생각했을때 안전하게 JavaScript를 사용하기 위해서는 트랜스컴파일러가 필요하다. 여기서는 가장 많이 쓰이는 트렌스컴파일러인 바벨을 빌드 도구인 걸프와 함께 사용해서 ES6 코드를 ES5 코드로 변환하는 방법에 대해서 알아볼 예정이다.

> ES5와 ES6의 차이에 대해서 공부할때 [Difference between ES 5 and ES 6](https://medium.com/@muthuks/difference-between-es-5-and-es-6-e993c7ab0a70) 아티클을 참고했다.

### 프로젝트 파일 만들기

변환전 작성한 ES6 코드와 변환 후 저장될 ES5 코드를 구분하기 위해 파일을 생성할 필요가 있다. 프로젝트 파일안에 ES6코드를 `es6`작성할 폴더와 저장될 ES5 코드를 위한 파일 `dist`도 만들자. (distribution의 약자) es6 폴더로 이동후 ES6코드를 작성할 test.js 파일을 만들자.

```bash
$ mkdir es6 dist
$ cd es6
$ touch test.js
```

> 만들어진 test.js 파일에 원하는 코드를 ES6로 작성해봅시다.

### package.json 생성

다시 프로젝트의 상위 디렉토리로 이동한 후 package.json 파일을 생성하자. 프로젝트의 이름과 버전등을 입력하고 yes하면 된다.

```bash
$ npm init
```

개발 의존성을 위한 devDependencies 라인을 추가하자.

```bash
$ npm install --save-dev gulp
```

### 패키지 가져오기

npmjs 웹사이트에서 우리가 원하는 [gulp-babel](https://www.npmjs.com/package/gulp-babel)패키지를 가져옵시다. 링크로 들어가면 설치와 사용법을 친절히 알려주고 있으니 우선 설치부터 하자. 

```bash
$ npm install --save-dev gulp-babel @babel/core @babel/preset-env
```

### gulpfile.js에 코드 입력

설치를 했다면 이제 걸프작업 코드를 입력할 차례다. 우선 코드를 입력할 gulpfile을 프로젝트 상위 디렉토리에 만들자.

```bash
$ touch gulpfile.js
```

그리고 만들어진 자바스크립트 파일안에 패키지 링크 `Usage`란에 있는 코드를 열심히 복붙하자. 복사한 코드에는 경로가 `'src/app.js'`지만 사용자의 설정에 따라 경로를 바꿔주면 된다.

### 변환하기

코드 수정을 완료했으면 드디어 변환할 차례다. 아래 명령어를 터미널에 입력하자.

```bash
$ gulp
```

> 프로젝트의 `dist`폴더 안에 `test.js`파일이 생성되었다면 성공!

### 마무리

트렌스컴파일러가 바벨만 있냐고 하면 또 그건아니다. `트레이서`라는 트렌스컴파일러가 있지만 [둘을 비교하는 글](https://ilikekillnerds.com/2015/01/transpiling-wars-6to5-vs-traceur/)을 보고 바벨을 선택했다. 걸프와 함께 사용하니 쉽고 간단하게 변환이 가능하다는점이 좋았다. 다음 시간에는 코드를 정돈해주는 린트프로그램인 `ESLint`에 대해서 알아보자 !
