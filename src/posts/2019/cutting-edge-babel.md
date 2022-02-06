---
title: Cutting-Edge JavaScript with Babel
description: ES6코드로 모든 브라우저 평정하기
date: 2019-07-01
tags:
  - posts
layout: layouts/post.njk
---

ES6코드로 새로운 앱을 만들고 IE에서 클릭했더니 하얀 화면이 나를 반긴다. 그렇다.
어떤 브라우저에서는 ECMA script 2015를 지원하지 않는다. 그러면 화살표 함수같은
ES6코드를 안쓰면 되잖아? 라고 생각할 수 있지만 좋은 생각이 아니다. 바벨을 통해
우리의 코드는 (거의)모든 브라우저에서 읽어들일 수 있는 코드로 바꿀 수 있다.

[babeljs.io](https://babeljs.io/)페이지의 `Try it out`탭에서 직접 코드를 작성하
고 어떻게 컴파일 되는지 볼 수 있다. 이번 글을 작성하면서
[프론트엔드 기술 조감도 : Babel](https://ahnheejong.name/articles/frontend-birds-eye-view-babel/)를
참고했다.

## Set up

먼저 바벨을 설치하자!

```bash
$ npm install -g babel-cli@version
```

input.js라는 이름의 파일을 읽은 후 output.js라는 이름으로 output을 만들어보자.

```bash
$ babel input.js -o output.js
```

그러면 output.js라는 새로운 파일이 생겼음을 확인할 수 있다. 하지만 안의 내용이
바뀐것은 없는데, Babel이 무엇을 할건지 알려주기 위해서는 `babel plug in`을 설치
해야한다. npm module을 프로젝트 루트에 local로 설치해 package.json 파일을 생성하
자.

## babel-preset

```bash
$ npm install babel-preset-env@version
```

위 명령어를 cmd에 입력하고 나면 프로젝트 디렉토리에 node_modules 이름으로 새로운
폴더가 생긴다. package.json파일안에도 `dependencies`라는 이름의 새로운 property
가 생긴다. package-lock.json 파일도 생성되는데 이 파일은 나중에 충돌이 생기지 않
도록 module의 버전을 계속 추적하는 역할을 한다. 그렇다면 우리가 설치한 preset을
이용해서 다시한번 cmd에 babel을 실행시켜 보자!

```bash
$ babel input.js -o output.js --presets env
```

이 명령어를 실행하고 나면 ES6에서 ES5로 변환된 파일을 볼 수 있다. 변환된 파일은
public 폴더로, ES6로 작성한 코드가 담겨있는 파일은 src 폴더에 넣자. index.html의
스크립트 태그를 public 폴더에 있는 스크립트 파일로 바꿔주는 것도 잊으면 안된다.

## 긴 명령어를 줄여보자

```bash
$ babel src/index.js --out-file public/scripts/bundle.js --presets env
```

위 명령어는 src폴더에 있는 index.js(ES6코드로 우리가 작성한 스크립트 파일)을
public폴더 안의 scripts폴더안에 bundle.js 이름으로 변환해 저장해달라는 뜻이다.
그렇다면 코드를 작성하고 변환할때마다 저렇게 긴(!) 명령어를 입력해야되냐고 묻는
다면 아니요. 라고 대답할 수 있다. package.json 파일에 scripts 태그를 만들어주면
된다.

```javascript
"scripts": {
  "build": "babel src/index.js --out-file public/scripts/bundle.js --presets env"
}
```

그러면 다음부터는 cmd에 `npm run build`라고만 입력하면 된다. 그런데 여기서 문제
가 또 있다. 코드를 작성하고 실시간으로 확인하기 위해 쓰는 `live-server`를 멈춰서
코드를 컴파일하고, 다시 live-server를 시작하기엔 너무 많은 수고가 든다!
package.json에 들어가서 새로운 옵션을 만들자. 아까 적었던 script에 `--watch`를
붙여주면된다.

```javascript
"scripts": {
  "build": "babel src/index.js --out-file public/scripts/bundle.js --presets env--watch"
}
```

## npm install

다른곳에서 코드를 가져왔을때 package를 설치하고 싶으면 `npm install`을 cmd에 실
행해서 받으면 된다. 해당 프로젝트 안에 있던 package dependency를 살펴보고 필요한
패키지가 자동으로 설치될것이다.

> node_modules 폴더는 이때 생성되는것이므로 다른 곳에 프로젝트를 업로드 할 때에
> 는 삭제 후 올려주면된다.

## Global module을 피해야 하는 이유

내 컴퓨터에는 live-server와 babel-cli가 global로 설치되어 있지만 내 프로젝트 파
일을 받아서 보는 사람 컴퓨터에는 없을 수도 있다. 그러면 실행이 안 될 것 이 야.
script에 short cut으로 저장해둔 명령어는 live-server와 babel-cli가 설치되어있다
는 전제하에 저장해둔것이기 때문이다. 이 문제를 해결하기 위해서는 babel,
live-server를 local dependencies에 추가해야한다. (물론 global이 있으면 안해도 된
다. 아래 예제를 따라하고싶으면 uninstall 해야한다!)

```bash
$ npm install babel-cli@version live-server@version
```

그렇다! g flag가 없으면 local로 다운로드 되는것이다. 명령어를 실행시키고 나면
package.json 파일의 dependencies에 추가된것을 볼 수 있다. 그리고 나서 이제
scripts에 있는 `npm run build`를 cmd에 입력하면 된다.
