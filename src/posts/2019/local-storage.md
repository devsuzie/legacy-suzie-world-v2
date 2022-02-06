---
title: Cross-tab communication with local storage
description: 로컬 스토리지를 사용해 탭끼리 정보를 공유해보자
date: 2019-07-29
tags:
  - posts
layout: layouts/post.njk
---

카카오 지도 api를 사용해 여행앱을 만들고 있던 중 문제에 봉착했다. 지도에서 장소를 검색하는 `map.html`페이지에서 검색 결과로 나온 장소이름을 `index.html`에 어떻게 전달하느냐 하는 문제였다. 즉 서로 다른 탭에서 같은 정보를 공유하는 커뮤니케이션의 문제였는데 지금 생각하면 간단하지만 구글링하기전 혼자 삽질하던 그시간들을 잊을 수 없다. 그래서 이번 글은 서로 다른 탭에서 소통(?)하는 방법에 대해 간단한 예시와 함께 적어볼까 한다.

## 예시 
`index.html` 파일에 있는 문장을 `test.html`으로 전달하는 예시를 만들어보자. 인덱스 페이지에서 `save`버튼을 클릭하면 비어있던 test페이지에 인덱스 페이지에 있던 문장이 나타나는 형식이다. 이 예시에 나오는 코드는 [깃헙 레포지토리](https://github.com/iamsuzie/local-storage)에서 확인할 수 있다.

## HTML
인덱스 페이지의 HTML 먼저 살펴보자. 인덱스 페이지와 테스트 페이지를 쉽게 확인할 수 있도록 네비게이션을 만들었고, `Hello World`라는 문장을 `p`태그에 작성했다. 저장 버튼을 누르면 test 페이지에 글자가 나타나도록 `button`태그도 만들었다. body 태그만 첨부한 아래 코드를 확인하자.
```html
<body>
    <nav>
        <ul>
            <li><a href="/index.html">Index</a></li>
            <li><a href="/test.html">Test</a></li>
        </ul>
    </nav>
    <h1>Index Page</h1>
    <p>Hello World</p>
    <button>Save</button>
    <script src="app/functions.js"></script>
    <script src="app/app.js"></script>
</body>
```

테스트 페이지의 HTML에는 인덱스 페이지와 마찬가지로 네비게이션과 인덱스 페이지의 'Hello World'가 옮겨질 빈 `span`태그를 만들어줬다. `button`태그를 만들어 클릭하면 로컬스토리지가 비워지도록 할것이다.
```html
<body>
    <nav>
        <ul>
            <li><a href="/index.html">Home</a></li>
            <li><a href="/test.html">Test</a></li>
        </ul>
    </nav>
    <h1>Test Page</h1>
    <span></span>
    <button>Clear</button>
    <script src="app/functions.js"></script>
    <script src="app/app-test.js"></script>
</body>
``` 

## CSS
```css
html, body{margin: 0; padding: 0; height: 100%;}
body{max-width: 340px; margin: 0 auto}
nav{display: inline-block; float: right; height: 45px; line-height: 45px}
nav ul{margin: 0; padding: 0; list-style-type: none;}
nav li{display: inline-block; margin: 0 7px}
nav li a{color: #221122; text-decoration: none}
span{display: block; margin: 16px 0;}
button{border: 1px solid #212121; border-radius: 5px; height: 25px; line-height: 25px; background-color: #c6dcee; font-size: 15px}
button:hover{background-color: #aac5db}
```

## JavaScript
자바스크립트 파일은 두개의 html 파일에 공통으로 들어갈 함수들을 담을 `functions.js`, 인덱스 파일에 적용될 `app.js` 그리고 test.html에 적용될 `app-test.js`로 총 세개를 만들었다. 공통으로 들어갈 `functions.js`파일을 먼저 살펴보자.

```javascript
const saveItem = (p) => {
  localStorage.setItem('item', p.innerHTML)
}

const removeItem = (span) => {
  localStorage.clear(span)
}
```

파일을 나눈게 무안할 정도로 적은 코드에 콧물이 나지만 습관을 들이는 중이니 이해해주세요. `saveItem`함수를 보면 local storage에 `item`이라는 이름으로 정보를 저장했다. 저장할 정보의 내용은 p.innerHTML이다. `removeItem`함수는 로컬스토리지에 저장된 정보를 삭제해주는 함수이다. 다음으로는 app.js 파일을 보자

```javascript
const button = document.querySelector('button')
const p = document.querySelector('p')

button.addEventListener('click', () => {
    saveItem(p)
    location.assign('/test.html')
})
```

인덱스 페이지의 버튼, 즉 `save`버튼을 클릭하면 `p`에 담긴 문장('Hello World')가 로컬스토리지에 저장되고, test.html로 경로를 재설정한다. 그렇다면 app-test.js에는 로컬 스토리지에 저장된 정보를 불러오는 코드가 적혀있겠지? app-test.js 파일을 확인해보자.

```javascript
const span = document.querySelector('span')
const button = document.querySelector('button')

span.textContent = localStorage.getItem('item')

button.addEventListener('click', () => {
    removeItem(span)
    location.assign('/test.html')
})
```

test.html 파일의 빈 span 태그는 로컬스토리지에 `item`이라는 이름으로 저장된 정보를 담는다. `clear`버튼을 클릭하면 로컬스토리지가 비워지고 (span 태그의 문장도 없어질것이다) 새로고침된다.

## 마무리
끝이다. 너무 간단해서 눈물이 나려고한다. 혼자서 개발 공부를 몇개월동안 하면서 느낀거지만 '무엇을 모르는지 모르는 상태'에서 '무엇을 모르는지 아는 상태'로 가는것이 중요한 것 같다. 무엇을 모르는지 조차 모르면 다음 단계에서 어떻게 행동해야할지 갈피를 못잡고 무한정 삽질을 하게 된다. 하지만 무엇을 모르는지 아는 상태에 도달하면 적어도 제대로된 질문을 할 수 있게 되어 답을 찾을 수 있게 되는것 같다.