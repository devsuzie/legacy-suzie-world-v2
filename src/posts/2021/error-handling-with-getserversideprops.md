---
title: NextJS 서버사이드 에러 핸들링
description: 스르륵(SSR) 에러 핸들링 ...
date: 2021-10-24
tags:
  - Programming
layout: layouts/post.njk
------

getServerSideProps를 사용할때 에러 핸들링은 어떻게 해야할까? pages 디렉토리 아래에 있는 `_error` 페이지와 `404` 페이지의 역할은 무엇일까? SSR  에러 핸들링을 어떻게 하는게 좋을까? 등등 많은 질문들에 대한 답을 찾아보자. 

## json-server로 테스팅 API 만들기

에러 핸들링을 위해서는 테스트를 해볼 수 있는 API가 필요하다. [public-api](https://github.com/davemachado/public-api) 등을 사용할 수도 있지만, authentication이 없는 대부분의 무료 api는 횟수 제한이 있기 때문에 나는 직접 로컬에서 API를 만들어서 자유롭게 테스트 해보는 방법을 선택했다.  [json-server](https://github.com/typicode/json-server)를 사용하면 json 파일 하나의 생성으로 원하는 API를 테스트해볼 수 있다. README에 사용 방법이 상세하게 나와있으니 읽어보기를 추천한다. 

우선 json-server를 글로벌하게 설치한다. 

```bash
npm install -g json-server
```

그리고 원하는 json 파일을 폴더안에 생성 후 해당 디렉토리에서 아래의 명령어를 실행하면 기본 포트인 3000으로 API가 생성된것을 확인할 수 있다.   

![1.png](https://user-images.githubusercontent.com/40863240/138596912-9b6671d5-089a-4e53-81c5-63556d5ec973.png)

나는 웹 프로젝트에서는 3000을 써야하는 3000포트 거주민이므로 서버 포트는 4000으로 옮겼다. `json-server.json` 파일에서 설정할 수 있다. 

```bash
{
  "port": 4000
}
```

나는 Next 공식 문서에 소개된 stars를 찍어보는 테스트를 할 예정이기 때문에 `db.json` 파일에 데이터는 아래와 같이 생성했다. 다시 한번 해당 파일을 실행하고, [`http://localhost:4000/response`](http://localhost:4000/response) 를 확인해보면 설정한 데이터가 잘 보이는것을 확인할 수 있다. 

```jsx
{
  "response": {
    "id": 0,
    "stars": 1237
  }
}
```

포트 설정과 response 확인까지 완료했다면 우리의 사실상 목적인 에러 핸들링을 하러 출동해보자.  Custom Output 설정은 `server.js` 파일에서 가능하다. 해당 파일을 생성하고 아래와 같이 설정해주면 된다.

> README의 [Custom output example](https://github.com/typicode/json-server#custom-output-example) 섹션에서 해당 내용과 관련된 부분을 확인할 수 있다.
> 

```jsx
const jsonServer = require('json-server')
const server = jsonServer.create()
const router = jsonServer.router('db.json')
const middlewares = jsonServer.defaults()

server.use(middlewares)
server.use(router)
server.listen(4000, () => {
  console.log('JSON Server is running')
})

// 에러를 발생시키는 부분 
router.render = (req, res) => {
  res.status(404).jsonp({
    error: 'error message here',
  })
}
```

프로젝트를 실행하는 방법은 두 가지가 있다. 생성한 `server.js` 파일을 실행하거나 해당 json 파일을 바로 실행시키는 것이다.

```bash
// json 파일을 바로 실행
json-server {file-name}.json

// server.js 파일 실행 (custom output 설정시 사용)
node server.js
```

## Next 프로젝트 세팅

테스팅 API 준비도 완료되었다면 타입스크립트 버전으로 Next 프로젝트를 설치한다. 플래그로 프로젝트 이름을 붙여야 하는 CRA와 다르게 해당 명령어를 입력한 후에 터미널에서 프로젝트 이름을 입력할 수 있도록 되어있다. 

```bash
npx create-next-app@latest --typescript
```

## API Fetching

`index.tsx` 파일의 서버사이드에서 해당 로컬 API를 Fetching 하자. 이 단계에서 프로젝트에는 어떠한 에러 핸들링도 되어있지 않다. (e.g. _error.tsx 또는 404.tsx 파일) 

```tsx
// api fetching
export async function getServerSideProps() {
  const res = await fetch('http://localhost:4000/response')
  const json = await res.json()

  return {
    props: {stars: json.stars || 0},
  }
}

// Home component
const Home = () => {
  return (
    // ...components
  )
}

export default Home
```

## 에러 확인

서버 API status를 404와 500으로 해주었을때 서버사이드 렌더링에서 발생한 에러이기 때문에 모두 500에러를 뱉으며 `unhandled error` 모달을 띄우는것을 확인할 수 있다.

![2.png](https://user-images.githubusercontent.com/40863240/138596915-59ac527b-6b6e-482c-b45d-f8fc8bfee6bd.png)

## 에러 핸들링

이 때 에러 핸들링은 어떻게 할 수 있을까? 우선 props에 status가 200일 경우의 응답값인 `stars` 뿐 아니라 `error` 객체도 넘길 수 있도록 하자. 나는 `title`과 `statusCode` 두 가지를 값으로 넣고 응답이 실패한 경우에만 해당 값들을 설정하도록 작업하였다. 

```tsx
export async function getServerSideProps() {
  const res = await fetch('http://localhost:4000/response')
  const error = res.ok
    ? false
    : {title: '에러가 발생했습니다', statusCode: res.status}
  const json = await res.json()

  return {
    props: {
      error,
      stars: json.stars || 0,
    },
  }
} 
```

그러면 이제 해야할 일은 간단하다. 해당 props를 전달 받는 컴포넌트 내에서 Error 컴포넌트를 반환하도록 하면 된다.  Next에서 제공하는 에러 페이지를 사용하고 싶다면 `Error` 컼포넌트를 import해서 사용할 수 있다.  위에 작성한 serverSide Fetching 로직과 타입 정의는 제외하고 코드를 작성하였다. 

> 전체 코드는 [next-anatomy](https://github.com/devsuzie/next-anatomy/tree/main/error-handling-with-getserversideprops) 레포에서 확인할 수 있다.
> 

```tsx

import Error from 'next/error'

// server-side fetching

const Home = ({error, stars}: Props) => {
  if (error) {
    return <Error statusCode={error.statusCode} title={error.title} />
  }

	return (
		// ...components
	)
}

export default Home
```

아래와 같이 Next에서 제공하는 Error 컴포넌트에서 설정한 statusCode와 title이 적용되어 보이는것을 확인할 수 있다. 

![3](https://user-images.githubusercontent.com/40863240/138596916-7b276d6b-d9a1-4362-9d42-902cbf21e172.png)

## 커스텀 에러

만약 Next의 Error 컴포넌트가 아닌 내가 설정한 컴포넌트를 에러 시에 보여주고 싶다면 어떻게 해야할까? 간단하다. pages 디렉토리에 `_error` 파일을 만들어주면 된다. 공식 문서에서 사용된 예제 파일을 그대로 사용해보겠다.

```tsx
function Error({ statusCode }) {
  return (
    <p>
      {statusCode
        ? `An error ${statusCode} occurred on server`
        : 'An error occurred on client'}
    </p>
  )
}

Error.getInitialProps = ({ res, err }) => {
  const statusCode = res ? res.statusCode : err ? err.statusCode : 404
  return { statusCode }
}

export default Error
```

next/error 가 아닌 커스텀 에러 컴포넌트를 Import 후 실행하니 설정한 문구가 잘 보이는것을 확인할 수 있다.

![4](https://user-images.githubusercontent.com/40863240/138596917-b684e929-f22d-4248-a79d-492e525d1cdb.png)

주의사항: 404 페이지를 만들지 않고 Error 컴포넌트를 만든다면 `warning`이 나타나기 때문에 Error 컴포넌트를 만들때는 404 페이지도 생성하도록 한다. [참고 문서][https://nextjs.org/docs/messages/custom-error-no-custom-404]

## 페이지마다 에러 처리를 해야할까?

아니다. 모든 `pageProps` 를 받아서 감싸고 있는 `_app.tsx` 파일에서 pageProps에 error가 있는 경우만 처리해준다면 문제는 해결된다. 바로 아래와 같이 할 수 있다.  

우선 위에서 에러 처리를 해준 페이지로 가서 해당 에러 처리 로직을 모두 제거한다. 그리고 `_app.tsx` 파일에 아래와 같이 에러 처리를 위임하도록 한다. 새로고침해보면 에러 화면이 나타나면서 에러 핸들링이 잘 되고 있음을 알 수 있다.

```tsx
function MyApp({Component, pageProps}: AppProps) {
  if (pageProps.error) {
    return (
      <Error
        statusCode={pageProps.error.statusCode}
        title={pageProps.error.message}
      />
    )
  }

  return <Component {...pageProps} />
}

export default MyApp
```

## 마무리

기본적으로 사용되는 로딩과 에러 화면만을 사용하다 서버에서 임의로 내려주는 에러 `Status 400`일 경우에는 기존 에러 화면과 다르게 다이얼로그 에러뷰를 보여주어야 하게 되면서 SSR 에러 핸들링에 대해 공부할 기회가 생겼다. 처음에는 페이지마다 처리하다가 이건 아니라는 생각이 들어서 해당 글을 작성하게 되었다. 이러한 경우 이외에도 에러코드나 메시지마다 다른 에러뷰를 띄워주어야 하는 상황일 경우 유용하게 사용될 것 같다. 공식 문서를 차근 차근 읽으면 해결할 수 있는 문제이지만 나와 같은 고민을 하는 사람들이 이 글을 보고 해결할 수 있게 되었다면 좋겠다! API를 테스트하기 위해서 어떻게 할 수 있을까 고민하다 발견한 `json-server` 도 넘나 소중한 것. 그럼 이만!