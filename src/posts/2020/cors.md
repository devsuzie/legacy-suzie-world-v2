---
title: CORS
description: Cross-Origin Resource Sharing
date: 2020-08-14
tags:
  - Programming
layout: layouts/post.njk
------

개발을 하다 보면 한 번쯤 마주하게 되는 CORS 에러.. 항상 서버 개발자님에게 CORS 에러가 나타났다! 도와주세요! 를 외치다 이게 무엇인가 알아보고자 작성하게 된 글이다. 내가 작업하고 있는 프로젝트의 클라이언트 단 웹 브라우저는 포트 4000에서 열리게 되어있고, 서버는 포트 3000을 사용하고 있다. 이때 클라이언트 쪽에서 서버의 데이터를 받아오려고 요청하면 `CORS` 에러가 나는데..! 이를 어떻게 해결할 수 있을까?

> 이번 글을 작성하면서 참고한 문서는 [Cross-Origin Resource Sharing (CORS) - HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)이다.

##  CORS란
`CORS`는 Cross-Origin Resource Sharing의 약자이다. 한국말로 번역하자면 교차 출처 리소스 공유. 한국말이 맞나 싶을 정도로 무자비한 단어들의 나열처럼 느껴진다.

MDN에서는 교차 출처 리소스 공유에 대해서 이렇게 설명한다.

> 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제

즉, CORS는 도메인 또는 포트가 다른 서버의 자원을 요청하는 메커니즘이다.  이를 요청할 때는 cross-origin HTTP에 의해 요청된다. 또한 다른 출처의 리소스를 불러오려면 그 출처에서 올바른 CORS 헤더를 포함한 응답을 반환해야 한다.

## same-origin policy (동일 출처 정책)
역시 MDN에서는 동일 출처 정책에 대해서 어떻게 말하고 있는지 살펴보자.

> [동일 출처 정책](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)은 어떤  출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식입니다. 동일 출처 정책은 잠재적으로 해로울 수 있는 문서를 분리함으로써 가능한 공격 경로를 줄이는 데 도움을 줍니다.

보안상의 이유로, 브라우저는 스크립트에서 시작한 교차 출처 HTTP 요청을 제한한다. 예를 들어, XMLHttpRequest와  Fetch API 동일 출처 정책을 따른다. 즉, 이 API를 사용하는 웹 애플리케이션은 자신의 출처와 동일한 리소스만 불러올 수 있으며, 다른 출처의 리소스를 불러오려면 그 출처에서 올바른 CORS 헤더를 포함한 응답을 반환해야 한다.

이것이 바로 내가 위에서 언급했던 CORS 에러가 나타난 이유이다. 올바른 CORS 헤더를 포함한 응답을 반환하면 에러가 해결되겠군! 하는 생각이 든다. 그런데 어떻게 그렇게 할 수 있지…?

## CORS가 동작하는 방식
나처럼 서버와 클라이언트를 분리하는 환경에서  CORS 에러가 나타났다면 어떻게 해결할 수 있는지 알아보자. 해결하기 위해서 먼저 알아야할것은 바로 CORS가 동작하는 방식이다. MDN 문서는 세 가지 시나리오를 제시하고 있으며, 내가 직면한 CORS 에러는 가장 마지막 시나리오와 관련이 있어 이를 파악하자마자 쉽게 해결할 수 있었다! 이제 이 세 가지 시나리오를 살펴보자. 

### Simple Request
 정식 명칭은 없지만 MDN에서 Simple Request라고 칭하고 있는 이 시나리오는 다음 조건을 모두 충족하는 요청이다. 

- GET / HEAD / POST 중 하나의 메서드여야 한다
- 설정할 수 있는 헤더는 `Accept`,  `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink`, `Save-Data`, `Viewport-Width`, `Width` 뿐이다
- `Content-Type` 헤더는 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 값만 허용한다

Simple Request는 예비 요청 없이 서버에 요청을 바로 한다. 서버가 이에 대한 응답 헤더에 Access-Control-Allow-Origin 값을 보내주면 그때 브라우저가 CORS 정책 위반 여부를 검사하는 시나리오다. 리소스에 대한 접근을 허용하려면, Access-Control-Allow-Origin 헤더에는 요청의 Origin 헤더에서 전송된 값이 포함되어야 한다.

### Preflight Request
Preflight Request는 위에서 살펴본 Simple Request와 달리 `OPTIONS` 메서드를 통해 서버에 예비 요청을 한다. 다른 도메인의 리소스스로 HTTP 요청을 보내 실제 요청이 전송하기에 안전한지 확인하는 것이다. 이처럼 안전한지 확인하는 예비 요청을 preflight 라고 한다. 

이 요청은 우선 preflight request/response 통신을 한 후, 이 요청이 완료되면 실제 요청을 전송한다.  MDN에 나와 있는 예제를 살펴보자. 

```bash
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
```

`Access-Control-Allow-Origin`는 서버가 리소스에 접근이 가능한 출처를 명시해주는 것이다. 요청을 보낸 출처와 이 출처를 비교해서 다른 출처임을 판단하게 되면 브라우저는 CORS 에러를 낸다.

### Request.credentials
Request 인터페이스 `credentials` 읽기 전용 속성이다. cross-origin 요청의 경우, user agent가 다른 도메인으로부터 cookie들을 전달해야만 하는가 아닌가를 나타낸다. 세가지 값이 사용 가능한데 아래에서 살펴보자.

- `omit` 
	- 절대로 cookie들을 전송하거나 받지 않는다
- `same-origin`
	- URL이 호출 script와 동일 출처에 있다면 user credentials (cookies, basic http auth 등..)을 전송한다. 이것은 default 값이다.
- `include`
	- cross-origin 호출이라 할지라도 언제나 user credentials 를 전송한다.

### 마무리
나는 세 가지 시나리오 중 가장 마지막인 Request.credentials 에 속했다. 작업하고 있는 프로젝트는 데이터를 fetch하기 위해서 [ky](https://github.com/sindresorhus/ky)라는 라이브러리를 사용하고 있었는데, 이 라이브러리의 `credentials`  기본 옵션은 `same-origin`이였기 때문에 CORS 에러가 나타났던 것이다. 옵션을 `includes`로 변경함으로써 문제는 간단하게 해결되었다. 기쁘다 기뻐~! 이 문서를 작성하면서 [HTTP 완벽 가이드](http://www.yes24.com/Product/Goods/15381085) 책을 읽어보게 되었는데, 다 읽어보고 재밌게 읽은 부분은 블로그에 공유해보고 싶다. 