---
title: Cookie & Session
description: 쿠키와 세션에 대한 이야기, 그리고 jwt.
date: 2020-06-17
tags:
  - Programming
layout: layouts/post.njk
------

새로운 관리자 페이지 작업을 하던 중이었다. sign in 후에 서버에서 데이터를 받아오려고 요청을 보내니 `401 unauthorized` 에러가 발생했다! 사수님에게 이유를 물어보니 쿠키에서 문제가 발생했으며 심어둔 쿠키는 유호한 기간동안 모든 api에 대해 알아서 딸려가게 되어있다며 이런 구조에 대해서 공부해보라고 하셨는데.. 드디어 쿠키와 마주할 때가 온 것인가. `쿠키`와 `세션`, 그리고 쿠키와 세션을 사용할 때 발생하는 문제들을 보완한 인증방식인 `jwt`에 대해서 알아보자!

## cookies

### 쿠키가 뭐죠

쿠키는 웹 브라우저 (클라이언트) 로컬에 저장되는 작은 `데이터 조각`이다. 키와 값으로 이루어져있으며, 쿠키는 클라이언트의 상태 정보를 로컬에 저장했다가 참조한다. 사용자 인증이 유효한 시간을 명시할 수 있으며, 유효 시간이 정해지면 브라우저가 종료되어도 인증이 유지된다. 상태가 없는, `stateless` HTTP 프로토콜에서 상태 정보를 기억시켜주는 역할을 한다. 즉, 서버가 사용자가 이전에 어떤 일을 했는지에 대한 정보를 유지할 수 있도록 해주는것이 쿠키다.

### 쿠키의 목적

- 세션 관리 (Session management)
  - 서버에 저장해야 할 로그인, 장바구니, 게임 스코어 등의 정보 관리
- 개인화 (Personalization)
  - 사용자 선호, 테마 등의 세팅
- 트래킹 (Tracking)
  - 사용자 행동을 기록하고 분석하는 용도

### 쿠키의 동작 방식

클라이언트가 페이지를 서버에 요청하면 (1) 서버에서 쿠키를 생성한다. 서버는 생성한 쿠키를 HTTP 헤더에 포함헤 함께 보낸다(2). 그 후 클라이언트에서 HTTP 요청을 서버에 보낼 때 헤더에 쿠키와 함께 보낸다 (3). 서버에서 쿠키를 확인하고 요청에 대한 응답을 보낸다 (4). 브라우저가 종료되어도 쿠키 만료 기간이 있다면 클라이언트에서 쿠키를 보관한다.

![cookie-flow](https://imgs.developpaper.com/imgs/372034436-5c359860bfdca_articlex.png)

> 이미지 출처: [Thoroughly understand session, cookie, token](https://developpaper.com/thoroughly-understand-session-cookie-token/)

## session

### 세션이 뭐죠?

세션은 쿠키를 기반하고 있지만, 데이터 조각을 브라우저에 저장하는 쿠키와 달리 세션은 `서버 측에서 관리`한다. 서버에서는 클라이언트를 구분하기 위해 `세션 ID를 부여`하며 웹 브라우저가 서버에 접속해서 브라우저를 종료할 때 까지 인증상태를 유지한다. 사용자에 대한 정보를 서버에 두기 때문에 쿠키보다 보안성이 좋지만, 사용자가 많아질수록 서버 메모리를 많이 차지하게 된다는 문제점이 있다.

> 클라이언트가 Request를 보내면, 해당 서버의 엔진이 클라이언트에게 유일한 ID를 보내는데 이것이 `세션 ID`이다.

### 세션의 동작방식

클라이언트가 서버에 접속 시 `세션 ID를 발급` 받는다. 클라이언트는 `세션 ID`에 대해 쿠키를 사용해서 저장하고 가지고 있다. 클라이언트는 서버에 요청할 때 이 쿠키의 세션 ID를 서버에 전달해서 사용한다. 서버는 `세션 ID`를 전달 받아서 세션에 있는 클라이언트 정보를 가져온다. 이 클라이언트 정보를 가지고 서버 요청을 처리하여 클라이언트에게 응답한다.

## 쿠키와 세션

### 쿠키와 세션의 차이점

- 사용자의 정보가 저장되는 위치
  - 쿠키는 서버의 자원을 사용하지 않으며, 세션은 서버의 자원을 사용.
- 보안
  - 로컬에 저장되는 쿠키는 보안에 취약하지만, 세션은 쿠키를 이용해 `세션 ID`만 저장하고 구분/처리는 서버에서 하기 때문에 비교적 보안성이 좋다.
- 라이프 사이클
  - 쿠키도 만료시간이 있지만 파일로 저장되기 때문에 브라우저를 종료해도 계속해서 정보가 남아있을 수 있다. 반면 세션은 브라우저가 종료되면 만료시간에 상관 없이 삭제된다.
- 속도
  - 쿠키에 정보가 있기 때문에 서버에 요청시 속도가 빠르고 세션은 정보가 서버에 있기 때문에 비교적 느린 속도이다.

### 문제점

쿠키는 보안이 약하다는 문제점이 있고, 세션은 사용자의 수 만큼 서버 메모리를 차지한다는 문제점이 있다. 최근에는 이런 문제점을 보완한 토큰 기반의 인증 방식을 사용하고 있는데, 그 중 하나인 `jwt`에 대해 알아보자.

## JWT (Json Web Token)

### JWT는 뭐죠?

Json Web Token은 두 개체에서 JSON 객체를 사용해 정보를 안정성 있게 전달해주는 웹표준이다. `jwt`는 필요한 모든 정보를 자체적으로 지니고 있고, 토큰이 검증 되었음을 증명해주는 `signature`를 포함하고 있다. 웹 서버의 경우 HTTP 헤더에 넣어서 전달할 수도 있고, URL의 파라미터로 전달할 수도 있다. 또한 상태를 유지하는 `stateful`서버인 세션을 유지하는 웹서버와 다르게 jwt는 `stateless` 서버이다. 상태를 유지하지 않으므로 클라이언트측에서 들어오는 요청만으로만 작업을 처리한다.

### 토큰 기반 시스템의 작동 원리

유저가 사용자 정보 (아이디, 비밀번호)로 로그인을 하면 서버측에서는 해당 계정 정보를 검증한다. 계정 정보가 정확하다면 서버측에서 유저에게 `signed` 토큰을 발급한다. 클라이언트 측에서는 전달받은 토큰을 저장하고 서버에 요청할 때마다 토큰을 서버에 함께 전달한다. 서버는 토큰을 검증하고 요청에 응답한다.

### JWT의 생김새

```bash
헤더(header).내용(payload).서명(signature)
```

#### 헤더 (header)

헤더에는 두가지 정보를 지니고 있는데 토큰의 타입을 지정하는 `type`, 해싱 알고리즘을 지정하는 (signature에서 사용됨) `alg`가 있다.

```js
{“alg”:”HS256”, “type:”JWT”}
```

#### 정보 (payload)

payload 부분에는 토큰에 담을 정보가 들어있다. 정보의 한 조각을 `claim` 이라고 부르는데 name/value 한 쌍으로 이루어져있다.

클레임의 종류는 아래와 같이 세 분류로 나뉘어져있다.

- 등록된 registered 클레임
  - 토큰에 대한 정보를 담기 위해 이름이 이미 정해진 클레임 (모두 옵셔널)
- 공개 public 클레임
  - 충돌이 방지된 이름 필요. URI 형식.
- 비공개 private 클레임
  - 클라이언트, 서버 협의하에 사용되는 클레임 이름들.

#### 서명 (signature)

서명은 헤더의 인코딩값과, 정보의 인코딩 값을 합친 후 주어진 비밀키로 해쉬를 하여 생성한다.

## wrap-up

쿠키와 세션, JWT에서 알아보았다. 401 에러 해결을 위한 첫 걸음을 뗀 기분이다. HTTP에 대해서 더 깊게 공부해보고 싶다는 생각이 들었다. 다음엔 CORS 에러에 대해서도 공부해볼 생각이다. 안뇽.