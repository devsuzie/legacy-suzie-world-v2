---
title: Cutting-Edge JavaScript with Webpack
description: Webpack으로 스크립트 태그를 하나로 만들기
date: 2019-07-11
tags:
  - posts
layout: layouts/post.njk
---

webpack은 babel보다 많은일을 한다. babel을 실행하는 것 이외에도 다양한 기능이 있다. Webpack은 다양한 폴더에 흩어져있는 자바스크립트 파일들을 묶어서 하나의 자바스크립트 파일로 public directory에 저장한다.

## Set up webpack
프로젝트 디렉토리로 가서 cmd에 webpack을 local에 설치하자.

```bash
$ npm install webpack@latestversion webpack-cli@latestversion
```

package.json파일을 보면 dependencies에 `webpack`과 `webpack-cli`가 설치된것을 볼 수 있을것이다. 프로젝트의 상위에 `webpack.config.js` 이름의 파일 만들자. 파일 안에는 configuration detail을 적으면 된다. 어디에서 파일을 가져와서 어디에 어떤 이름으로 저장할지 등에 관한 내용이다. 아래 예시를 보자.

```javascript
const path = require('path')

module.exports = {
    entry: './src/index.js'
    ooutput: {
        path: path.resolve(__dirname, 'public/scripts'),
        filename: 'bundle.js'
    }
}
```

`__dirname`이라는 nodejs에서 제공하는 라이브러리를 사용해서 절대 경로를 우리 프로젝트에 제공해 줄 것이다. 

## npm run webpack
package.json 파일의 scripts 옵션에 `"webpack": "webpack"`을 추가하고 웹팩을 실행해보자.

```bash
$ npm run webpack
```

명령어를 실행하면 우리가 지정한 경로에 새로운 자바스크립트 파일이 생긴것을 볼 수 있을 것이다. 파일을 클릭해보면 오~ 사람으로써는 도저히 알아볼 수 없는 글자들이 난무한데 사실 이것은 index.js를 컴퓨터가 빨리 읽어 올 수 있도록 압축한것이다.

## Javascript module: Import & Export
webpack 에서 제공하는것중 하나는 `javascript module`이다. 이전에는 자바스크립트 태그를 하나의 html 파일에 여러개를 순서대로 지정해서 넣어야했다 (원하는 function을 꺼내쓰기 위해서는). 이제는 bundle.js에 모든게 다 포함되어 있을것이기 때문에 하나의 스크립트 태그로 해결할 수 있다.

어떻게 그렇게 할 수 있죠? 가령 우리가 src 디렉토리 안에 두개의 자바스크립트 파일을 가지고 있다고 해보자. 하나는 `index.js` 파일이고 다른 하나는 `utilities.js` 파일이라고 하면 index.js 파일 안에 다른 자바스크립트 파일을 `import`하면 된다. 
```javascript
import './utilities'
```

만약 `utiliteis.js`에서 어떤 함수 꺼내쓰고 싶다면 파일에 들어가서 export 해줘야한다.
```javascript
console.log('utilites.js')
export const add = (a, b) => a + b
```

그러면 이제 어떤 함수를 import할것인지 `index.js` 파일에서 특정해줘야한다.
```javascript
import { add } from './utilities'
```

## JavaScript Module: Export style
export style에는 두가지 방법이 있다. 하나는 `Named export` 다른 하나는 `Default export`이다. 
### Named export
```javascript
export const add = (a, b) => a + b
export const name = 'Suzie'
```
### Default export 
```javascript
const square = (x) => x * x
export default square
```

그러면 어떻게 default export를 import하느냐
named export처럼 이름으로 가져오는것이 아니라 default를 가져오는것이기 때문에 이름은 무엇이 되었든 상관 없다. 위 코드에서 sqaure을 export했지만 import할때 부르는 이름으로는 뭘 해도 상관없다. 나는 스폰지밥 스퀘어팬츠로..

```javascript
import squarepants, { add, name } from './utilities'
```

### 한방에 export 하기
Named export 그리고 Default export 두가지를 한방에 export 하고 싶다면 아래 예제를 참고하자. 
```javascript
const add = (a, b) => a + b
const name = 'Suzie'
const square = (x) => x * x

export { add, name, square as default }
```
