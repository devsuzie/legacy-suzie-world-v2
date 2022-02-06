---
title: Next.js로 SSG..
description: 넥스트, 이기적 성능의 비밀은 SSG ?!
date: 2021-06-28
tags:
  - Programming
layout: layouts/post.njk
------

NextJS는 서버 사이드 렌더링을 잘 사용할 수 있게 해준다. 뿐만 아니라 서버 사이드 렌더링과 클라이언트 사이드 렌더링을 함께 사용할 수도 있다. 이 때 잘 사용할 수 있게 한다는 말이 어떤 말일까? 또 `SSR`과 `CSR`은 무엇일까? 이번 글을 통해 알아보도록 하자.

## CSR과 SSR
### CSR (Client Side Rendering)
[create-react-app](https://github.com/facebook/create-react-app)을 통해 앱을 만들게 되면, `index.html` 파일에서 아래와 같은 코드를 확인할 수 있다. 

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
</body>
```

`CSR`을 기본으로 하는 리액트에서는 위와 같이 빈 HTML 파일을 서버가 클라이언트에게 보내주게 된다. 그 이후 `app.js` 파일을 요청하고 받아오면서 사용자에게 최종적인 앱을 보여주게 된다. 이 경우에는 유저가 같은 사이트 내에서 페이지를 이동해도 서버에서 새로운 HTML 파일을 받지 않고 클라이언트가 페이지를 그리기 때문에 화면의 깜빡임 없이 사용자가 사이트를 사용할 수 있다. 

하지만 번들링된 자바스크립트 파일의 크기가 크기 때문에 사용자가 화면을 보기 까지 시간이 오래 걸리게 되고, 빈 HTML 파일을 최초에 보내주기 때문에 SEO 대응이 좋지 않다. 정리해보자면 아래와 같다.

- 사용자가 첫 화면을 보기까지 오랜 시간이 걸린다.
- SEO 검색 엔진 최적화 대응이 좋지 않다.
- 인터렉션이 많은 페이지인 경우 사용자 경험이 좋다. (TTI와 TTV가 동시에 가능하다)

### SSR (Server Side Rendering)
`SSR`은 `CSR`과 달리 서버에서 필요한 데이터를 모두 가져와서 HTML 파일을 생성한다. 때문에 SEO 성능이 좋고, 첫 로딩 시간이 빨라진다. 사용자가 사이트를 볼 수 있는 시간인 `TTV` (Time To View)는 빠르지만 HTML 파일을 가져온 이후에 (페이지가 로드 된 이후에) Javscript 소스 코드가 실행되어 `TTI` (Time To Interaction)이 가능해진다. 정리하자면 아래와 같다.

- 사용자가 첫 화면을 빠르게 볼 수 있다.
- SEO 검색 엔진 최적화 대응이 좋다. 
- 페이지 이동 시 새로운 HTMl 파일을 받아오기 때문에 화면의 깜빡임이 있다.
- TTI와 TTV 시간의 공백이 길다. 

### CSR Vs. SSR
페이지가 렌더링 되는 곳이 어디인가에 따라 두 방식의 차이가 있다. `CSR`은 페이지를 클라이언트 사이드에서 렌더링하고, `SSR`은 페이지를 서버 사이드에서 렌더링 하게 된다. `CSR`을 사용한다면 최종적으로 번들링 되어서 사용자에게 보내지는 자바스크립트 파일을 어떻게 하면 잘 쪼개서 사용자가 페이지를 보기 위해 필요한 소스 코드만 보낼 수 있을지에 대한 고민이 필요할 것이다. 반면에 `SSR`을 사용한다면 `TTI`와 `TTV` 시간의 간격을 줄이기 위한 고민이 필요할 것이다. 

두 렌더링 방식에 있는 각각의 장점을 잘 사용할 수 있도록 지원해주는 프레임워크가 있는데, 그것이 바로 `Next.js`이다.

## NextJS
`Next.js`의 소개 글에서는 `Next.js`가 많은 내장 기능을 갖추고 있으면서, 최고의 개발 환경을 제공한다고 말하고 있다. 여기서 말하는 기능에는 `SSR`과 `CSR`을 함께 사용할 수 있도록 하는 기능, `SSR`과 `SSG` 단위로 지원되는 `pre-renders` 기능 등이 포함되어 있다. 갑자기 SSG가 뭐야 싶겠지만 궁금증 멈춰! 차례대로 살펴보자.

### SSR과 CSR을 함께 사용하기
먼저 `npx create-next-app` 명령어를 사용해 next app을 만들어보자. 나는 typescript를 사랑하기 때문에 typescript를 사용하여 만들것이다. 명령어를 실행하면 프로젝트의 이름을 무엇으로 할것인지 묻는데, `next-app`으로 설정했다.

![1](https://user-images.githubusercontent.com/40863240/123662720-addfe380-d870-11eb-9960-c6dc36f6c0b5.png)

pages 폴더의 `index.tsx`에 들어가서 예쁘게 짜여진 next 템플릿 코드를 지우고 `suzie.world`를 입력하고 실행해보았다. Network의 docs 탭에 들어가보면 서버 사이드 렌더링으로 입력한 내용이 포함된 HTML 파일을 볼 수 있다. 

![2](https://user-images.githubusercontent.com/40863240/123663819-bc7aca80-d871-11eb-9aca-f890816e44f8.png)

[next/link](https://nextjs.org/docs/api-reference/next/link) 또는 [next/route](https://nextjs.org/docs/api-reference/next/router) 를 사용하면 페이지 이동 시 `CSR`을 사용할 수 있다. pages 폴더 내에서 `hello.tsx` 파일을 만들고, `index.tsx` 파일에서 [next/link](https://nextjs.org/docs/api-reference/next/link)를 사용하여 라우트를 만들어줬다.

```html
<ul>
  <li>
    <Link href="/">
      <a>Home</a>
    </Link>
  </li>
  <li>
    <Link href="/hello">
      <a>Hello</a>
    </Link>
  </li>
</ul>
```

네트워크 탭을 열어 index 페이지에서 hello 페이지로 라우트 이동을 해보면 페이지 이동을 해도 새로운 요청이 추가되지 않은 것을 볼 수 있다.

![3](https://user-images.githubusercontent.com/40863240/123665284-1b8d0f00-d873-11eb-8dae-fa0b807eb92f.png)

이처럼 첫 페이지는 서버에서 받은 HTML 파일을 렌더링 하고 그 뒤에 발생하는 라우팅은 클라이언트 사이드 렌더링하여 SSR과 CSR을 함께 사용할 수 있다.

### SSR과 SSG 단위로 지원되는 pre-renders
Next.js 공식 문서에서는 SSG를 `Static-Generation`이라고 소개하고 있다. 기본적으로 Next.js는 모든 페이지를 사전 렌더링 (Pre-rendering) 한다. 넥스트는 두 가지 방식으로 사전 렌더링을 지원하는데 바로  `static generation`인 SSG와 `server side redreing` SSR이다. 두 가지 방식의 차이는 HTML 파일이 생성되는 시점이다.

#### Static Generation (SSG)
> 빌드 타임에 HTML 파일이 생성되며 매 요청시 재사용된다.

- [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)
  - 페이지의 컨텐츠가 외부 데이터에 의존하는 경우 사용된다.
  - 해당 비동기 함수를 사용하게 되면 Next.js는 `getStaticProps`에서 반환한 Props를 사용하여 빌드 시 이 페이지를 미리 렌더링하게 된다.

```js
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  return {
    props: {}
  }
}
```

- [getStaticPaths](https://nextjs.org/docs/basic-features/data-fetching#getstaticpaths-static-generation)
  - 페이지의 path가 외부 데이터에 의존하는 경우 사용된다.
  - 해당 함수를 사용하게 되면 Next.js는 `getStaticPaths에서`에서 지정한 모든 Path를 정적으로 미리 렌더링하게 된다.

```js
import { GetStaticPaths } from 'next'

export const getStaticPaths: GetStaticPaths = async () => {
  return {
    paths: [
      { params: { ... } }
    ],
    fallback: true or false
  };
}
```

#### ServerSide Rendering (SSR)
> 매 요청마다 새로 HTML 파일이 생성된다.

- [getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching#getserversideprops-server-side-rendering)
  - 페이지가 SSR을 사용할 경우 사용된다.
  - 해당 함수를 사용하게 되는 경우 Next.js는 `getServerSideProps`에서 반환한 데이터를 사용하여 각 요청에 이 페이지를 미리 렌더링한다. 

```js
export async function getServerSideProps(context) {
  return {
    props: {}
  }
}
```

문서에서 [언제 getServerSieProps를 사용해야 하나요?](https://nextjs.org/docs/basic-features/data-fetching#when-should-i-use-getserversideprops) 파트를 보면 해당 함수는 요청 시 데이터를 가져와야 하는 페이지를 미리 렌더링해야 하는 경우에만 사용해야 한다고 말하고 있다. 페이지에 동적으로 업데이트되어야 하는 데이터가 포함되어 있고, 데이터를 미리 렌더링할 필요가 없는 경우 클라이언트 측에서 데이터를 가져오는 방법을 추천한다고 말한다. 그리고 매끄럽게 이어지는 자사 제품 `SWR` 추천.. 하지만 나는 리액트 쿼리 쓸거야.

## 마무리
SPA CSR SSR SSG TTI TTV .. 그리고 티아라의 TTL. 회사에서 Next.js를 사용하면서 Next.js가 제공하는 pre-redering 기능을 공부하다 보니 1990년대 SSR의 시대부터 2020년대 SPA의 시대까지 역사를 거슬러 올라가게 되었다. 많은 불편함이 해소된 이후에 개발을 하다 보니 이전의 문제를 해결해주는 도구를 비교적 쉽게 손에 얻게 되었다. 그 때문에 좋은 도구를 사용하면서도 이 도구는 어떤 문제를 해결해주는 도구인지, 어떻게 써야 하는지 방법을 제대로 파악하지 못한 채 막연히 좋겠지! 하며 사용했던 적이 많았던 것 같다. 반성합니다.. 솔직히 새벽 한 시라서 잠이 와서 내가 뭐라고 하는지 잘 모르겠다. 이만 줄여야겠다. Next 최고!