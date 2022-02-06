---
title: gulp를 이용해 SCSS를 CSS로 변환하기
description: gulp가 뭔데.. 그거 어떻게 하는건데
date: 2019-04-20
tags:
  - posts
layout: layouts/post.njk
---

Sass(Syntactically Awesome Style Sheet)를 사용해 스타일 시트를 작성하면 코드의 가독성이 높아질뿐 아니라 코드의 재활용성을 높여주고 작업하기 쉽다는 장점이 있다. 하지만 브라우저는 Sass의 문법을 알지 못하기 때문에 css 파일로 변환해야한다. 변환 방법은 여러가지가 있지만 여기서는 반복 작업을 자동화 해주는 빌드 도구인 `gulp`를 이용한 방법을 소개할 예정이다.

### Node.Js 설치

`gulp`를 설치하기 위해서는 npm 명령어가 필요하다. npm(node package manager)은 Node.Js를 내려받을 때 동시에 설치된다. [Node.js공식홈페이지](https://nodejs.org/en/)에서 다운받을 수 있다. 

> 안정적으로 사용할 수 있는 왼쪽의 LTS(Long Term Supported) 버전의 다운로드 버튼을 누르자.

### gulp 설치

Node.Js를 성공적으로 설치했다면 터미널을 켜서 gulp를 본격적으로 설치해보자.

```bash
$ npm install -g gulp
```

> mac OS에서 사용한다면 -g(전역)에서 npm 플래그를 사용하기 위해 관리자 권한이 필요할 수 있다. 그럴때는 $와 npm사이에 `sudo`를 입력하자. 당신은 슈퍼유저 권한을 받게 됩니다.

### package.json 파일 생성

먼저 변환할 `.scss` 파일이 있는 프로젝트의 디렉토리로 이동 한다.

```bash
$ cd <file directory>
```

현재 폴더에 package.json 파일을 생성하자. 이 파일은 생성된 패키지들을 관리해준다. 

```bash
$ npm init
```

> 명령어를 입력하면 프로젝트의 이름과 버전, 설명 등을 입력할 수 있는 란이 뜨는데 입력하지 않아도 실행에 문제는 없다.

### devDependencies

프로젝트마다 로컬 걸프가 필요하므로 프로젝트 루트에서 아래 명령어를 실행하자.
걸프는 최종 사용자에게는 필요 없지만 개발 과정에서 도움 되는 개발 의존성에 속한다.
명령어를 실행하면 package.json 파일에 devDependencies 라인이 추가된다.

```bash
$ npm install --save-dev gulp
```

> dependencies와 devDependencies의 차이점은 [이 아티클](https://medium.com/@stalonadsl948/dependencies-vs-devdependencies-926e096a3dee)에서 볼 수 있다. 

### gulpfile.js 생성

이제 프로젝트 파일에 원하는 걸프 작업을 입력할 자바스크립트 파일을 만들어 보자.

```bash
$ touch gulpfile.js
```

> `touch`는 새로운 파일을 생성할 때 쓰이는 명령어다.

### 원하는 패키지 가져오기

npmjs 홈페이지에는 엄청나게 많은 패키지들이 존재한다. 내가 원하는건 sass파일을 css파일로 컴파일링 해줄 수 있는 [gulp-sass](https://www.npmjs.com/package/gulp-sass) 패키지다.
링크를 통해 들어가면 친절히 패키지 설치방법 부터 사용할 수 있는 코드가 적혀져있다. 터미널에 복붙하자.

```bash
$ npm install node-sass gulp-sass --save-dev
```

패키지 설치를 완료했으면 `Basic Usage` 내용에 있는 코드를 복사해서 내 프로젝트 폴더에 만들어 두었던 gulpfile.js에 붙여넣기 하자. 프로젝트 폴더 안에 있는 모든 sass파일을 변환할건지 또는 내가 설정한 하나의 sass파일만 변환할건지 살펴본 후 코드를 수정하자.

### 변환하기

코드 수정을 완료했으면 드디어 변환할 차례다. 아래 명령어를 터미널에 입력하자.

```bash
$ gulp sass
```

> css파일이 생성되었으면 성공한것이다 !

### 마무리

자바스크립트에서 가장 널리 쓰이는 빌드 도구인 `그런트`와 `걸프`중 어떤걸 사용할까 하다가 걸프를 선택했다. 이 글에서는 걸프를 통해 스타일 시트 변환을 해 보았지만 실제로 빌드 도구를 통해서 할 수 있는일은 무궁무진하다. 여기서는 자세히 다루지 않은 package.json 에 대한 내용은 [모두 알지만 모두 모르는 package.json](https://programmingsummaries.tistory.com/385) 을 통해 공부했다. (공부 했는데도 아직 잘 모르겠다.) 다음에는 바벨을 걸프와 함께 사용하는 방법에 대해서 알아볼까 한다. 기대해주세요!