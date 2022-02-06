---
title: 메모앱을 만들어보아요
description: local storage와 JSON의 아름다운 만남
date: 2019-08-05
tags:
  - posts
layout: layouts/post.njk
---

나는 기록하는걸 정말 좋아한다. 쓸데 없는것부터 중요한것까지 다시 펼쳐보든 보지않든 무언가를 적을 수 있다는건 나에게 정말 큰 의미다. 모름지기 사람이 로컬스토리지의 사용법을 깨우쳤으면 메모앱 하나쯤은 만들어야하지 않겠는가. 흠흠. 그래서 이번에는 메모앱을 만들어보는 글이다. 아주 간단한 형식의 메모앱이다. 지난 글에서는 한개의 아이템을 로컬스토리지에 저장했다면 이번에는 여러개의 아이템을 저장할것이기 때문에 JSON이 필요하다는 것이다. 시작해봅시다! 
> 이번 예제 역시 [깃헙 레포지토리](https://github.com/iamsuzie/memo-app)에서 확인할 수 있다. 

## HTML
`section`태그를 팝업창으로 만들기위해 생성하고 안에 `textarea` 인라인 태그를 만들어 메모를 입력할 공간을 만들었다. 팝업창 안에 'save'버튼을 만들어 클릭하면 로컬스토리지에 메모 정보를 저장하게 만들 예정이다. 그렇게 로컬스토리지에 저장된 정보는 ul 태그안에 getItem으로 불러올것이다.
```html
<body>
    <h1>Memo</h1>
    <ul></ul>
    <button class="write-btn">Write</button>
    <section>
        <div>
            <h2>write and save</h2>
            <textarea cols="num" rows="num" spellcheck="false"></textarea>
            <button class="save-btn">Save</button>
        </div>
    </section>
    <script src="app/functions.js"></script>
    <script src="app/app.js"></script>
</body>
```

## CSS
section에 `on` 클래스가 들어가면 팝업창이 나타나고, `on`클래스가 빠지면 팝업창이 사라져 기존 메모장 모습이 나타나게 만들었다. 아래에는 팝업창이 나타난 메모앱의 스크린샷을 첨부했다.
![Screen Shot 2019-08-31 at 23.14.04](//images.ctfassets.net/5xgpncj4c37m/3HuWnNpLwXUglNvcbyWjPd/10879f91f9a065303fd0b67d2fe90a6c/Screen_Shot_2019-08-31_at_23.14.04.png)
```css
html, body{margin: 0; padding: 0; height: 100%;}
body{max-width: 340px; margin: 0 auto}
h1{padding: 20px 0;}
h2{text-align: center}
ul{list-style: none; margin: 0; padding: 0 0 30px 0}
li{border-bottom: 1px dashed #212121; line-height: 25px; padding: 7px 0}
button{display: block; border: 1px solid #212121; border-radius: 5px; height: 25px; line-height: 25px; background-color: #c6dcee; font-size: 15px}
button:hover{background-color: #aac5db}
button.save-btn{margin: 30px auto 20px;}
section{display: none; width: 100%; height: 100%; background-color: rgba(0, 0, 0, 0.5);; position: absolute; top: 0; left: 0;}
div{display: block; position: absolute; top: calc(20% - 20px); left: calc(50% - 150px); width: 300px; background-color: #ffffff; border: 1px solid #221122; border-radius: 5px}
textarea{display: block; margin: 0 auto; resize: none; width: 80%; height: 100px; border: 1px solid #212121; border-radius: 5px; padding: 10px 6px; font-family: 'Avenir'}
section.on{display: block;}
```

## JavaScript
자바스크립트 파일은 함수들을 담은 `functions.js`와 인덱스 파일에 들어갈 `app.js`파일 두개를 만들었다. functions.js 파일 먼저 살펴보자.

```javascript
const getSavedMemos = () => {
  const memosJSON = localStorage.getItem('memos')

  try {
    return memosJSON ? JSON.parse(memosJSON) : []
  } catch(e) {
    return []
  }
}

const saveMemos = (memos) => {
  localStorage.setItem('memos', JSON.stringify(memos))
}

const renderMemos = (memos) => {
  document.querySelector('ul').innerHTML = ''
  memos.forEach((memo) => document.querySelector('ul').appendChild(generateMemoDom(memo)))
}

const generateMemoDom = (memo) => {
  const memoLi = document.createElement('li')
  memoLi.innerHTML = memo.content
  return memoLi
}

const controlPopUp = () => {
  const popUp = document.querySelector('section')
  if (popUp.classList.contains('on')) {
    popUp.classList.remove('on')
  } else {
    popUp.classList.add('on')
  }
}
```

### 로컬스토리지에 저장하고 읽어오기
`saveMemos`함수는 메모를 로컬스토리지에 저장해주는 함수이다. 로컬스토리지에 아이템을 저장할때에는 value값이 문자열이여야한다. 그렇기 때문에 Object 배열을 로컬스토리지의 value값으로 저장하기 위해서는 문자열로 바꾸어주어야한다. 그 역할을 하는것이 바로 `JSON.stringify`이다. 그렇다면 저장한 문자열을 불러올때는 어떻게 해야할까? `JSON.parse`로 다시 배열로 바꾸어주어야한다. 그냥 문자열에서 꺼내 쓰면 안되나? 싶을 수 있지만,
배열이 아닌 문자열이기 때문에 `userJSON.method` 처럼 배열중 내가 원하는 key값만 꺼내 쓸 수 없다. 그래서 배열로 다시 parsing 해줘야하는것이다.

`getSavedMemos`에서는 로컬스토리지에 'memos'라는 key값의 정보가 있으면 가져와서 parsing해주고, 로컬스토리지에 아무것도 없으면 빈 배열 `[]`을 반환한다.

### 메모 Dom 생성하기
`generateMemoDom`함수는 index.html에서 빈 'ul'태그를 만들었던것을 기억하는가? 그 ul 태그안에 들어갈 각각의 메모 li를 형성하는 함수이다. `renderMemos`함수는 말그대로 랜더링해주는 함수이다. 새로운 정보를 랜더링하기전에 그전에 화면에 나타나있던 정보를 없애기 위해 `innerHTML = ' '`로 화면을 비워주자. 그 이후에는 memos안에 있는 각각의 memo를 index.html안의 ul 태그 안에 `generateMemoDom`으로 만들었던 li태그를 자식으로 넣어주자.

### 팝업창 제어하기
`controlPopUp`함수는 팝업창을 만들기 위한 함수이다. css파트에서 설명했듯이 `on`태그가 있냐 없냐에 따라 팝업창이 켜지고 꺼지고를 결정하기때문에 `on`태그를 클래스 리스트에서 없애고 추가하는 함수를 만들었다. 

aap.js파일안의 코드를 살펴보자.
```javascript
const writeBtn = document.querySelector('.write-btn')
const saveBtn = document.querySelector('.save-btn')
const text = document.querySelector('textarea')
const memos = getSavedMemos()

renderMemos(memos)

writeBtn.addEventListener('click', () => {
    controlPopUp()
})

saveBtn.addEventListener('click', () => {
    memos.push({
        content: text.value
    })
    saveMemos(memos)
    renderMemos(memos)
    text.value = ''
    controlPopUp()
})
```
렌더링 이후의 코드를 살펴보자. index.html파일의 `write`버튼을 클리하면 controPopUp 함수가 호출되 `on`클래스를 컨트롤할 수 있다. 팝업창에 있는 `save`버튼을 클릭하면 textarea안에 쓴 글이 로컬스토리지에 저장이된다. 로컬스토리지에 저장을 완료했으면 저장된 정보를 바탕으로 다시 랜더링 되고 textarea는 비워지고 팝업창이 꺼진다. 

## 마무리
로컬스토리지 하나의 지식을 가지고 만들 수 있는것은 정말 무궁무진한것 같다. 이번에는 비교적 간단한 메모앱을 만들어보았지만 나중에는 좀더 복잡한 기능을 가지고 있는 내가 쓰기에 딱 맞는 메모앱을 만드는게 목표다! 