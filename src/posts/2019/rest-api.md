---
title: 그런 REST API로 괜찮은가
description: REST가 무엇인지, 그리고 그 역사에 대해서 알아보자
date: 2019-11-01
tags:
  - posts
layout: layouts/post.njk
------

> DEVIEW 2017에서 응준님이 발표하신 `그런 REST API로 괜찮은가`를 정리한 글입니다. 발표 슬라이드는 [이곳](https://slides.com/eungjun/rest#/)에서, 영상은 [이곳](https://www.youtube.com/watch?v=RP_f5dMoHFc)에서 확인하실 수 있습니다.

## REST의 정의
REST는 REpresentational State Transfer의 약자이다.
위키백과에서는 'a way of providing interoperability between computer systems on the internet'라고 말한다.
컴퓨터 시스템 간의 `상호운용성`을 제공하는 방법 중 하나라는 뜻이다.
> 상호운용성(Interoperability)이란 하나의 시스템이 동일 또는 이기종의 다른 시스템과 아무런 제약이 없이 서로 호환되어 사용할 수 있는 성질을 말한다. (출처: [위키백과](https://ko.wikipedia.org/wiki/%EC%83%81%ED%98%B8%EC%9A%B4%EC%9A%A9%EC%84%B1))

## REST의 역사
1991년 버너스리에 의해 월드와이드웹이 탄생한 이후에 어떻게 인터넷에서 정보를 공유할 것인지에 대한 문제가 생겼다.
버너스리는 정보들을 하이퍼 텍스트로 연결함으로써 문제를 해결할 수 있다고 말했다.
그래서 HTML이라는 형식으로 정보를 표현하고, 그 정보들에 대한 식별자로 URL을 만들고, 그 정보들을 전송하는 방법으로 HTTP라는 프로토콜까지 만들었다.

HTTP라는 프로토콜을 여러 사람들이 설계하게 되었는데 그 중 한 명이 로이 필딩이다.
로이필딩은 어떻게 웹을 망가트리지 않고 HTTP를 진보시킬 수 있을까에 대한 고민을 했다.
그는 그 고민에 대한 해결책으로 `HTTP Object Model`을 만들었다.

이것이 4년 뒤인 1998년에 `REST`라는 이름으로 발표되었고 그로부터 2년 후에는 로이필딩이 박사 논문으로 발표를 했다.

한편 인터넷상에서 API라는것이 만들어지기 시작했다.
`마이크로 소프트`에서는 1998년 XML-RPC 프로토콜 만들었는데, 이것이 추후에 SOAP 이라는 이름으로 바뀌었다.
또 `Salesforce`라는 회사에서는 2000년에 인터넷에서 거의 최초로 공개된 API를 선보였다. SOAP을 사용해서 API를 만들었는데 너무 복잡해서 인기를 얻지 못했다.
4년 후인 2004년 `Flickr`에서도 API를 만들었다. SOAP 형태로 된 API 그리고 REST라는 이름의 API도 공개했다. 새로워 보이지만 로이필딩이 이미 발표했던 그 REST이다. REST는 우선 SOAP보다 훨씬 짧았다.

즉 SOAP은 복잡고, 규칙 많고 어렵다고 느꼈지만, REST는 단순하고 규칙 적고 쉽다는 느낌을 받게 되었다.
결국, SOAP은 인기가 갈수록 추락하고 REST는 인기 급상승했다.
2006년 AWS가 자사 API의 사용량 85%가 REST임을 밝혔고,
Salesforce.com은 2010년에 REST API를 추가했다.

즉 REST의 승리라고 할 수 있다.
그렇게 월드 와이드 웹이 REST로 정착이 되나 싶었는데…!

## 하지만 진정한 REST를 사용하는 곳은 없다?
2008년 CMS를 위한 표준인 CMIS가 나왔다. CMS는 Content Management System의 약자이다. 즉 콘텐츠 관리 시스템을 말한다. CMIS는 Content Management Interoperability Services의 약자이다.
EMC, IBM, MS등이 함께 작업했고 REST 바인딩을 지원하지만 이런 CMIS에 REST는 없다라고 로이필딩은 말했다.

2016년 마이크로 소프트가 uri 형식 등이 포함된 REST API 가이드 라인을 만들었다.
하지만 로이필딩이 이것 또한 REST API가 아니라 HTTP API/라고 말했다. 덧붙여 REST API는 반드시 `hypertext driven`이여야 한다고 한다.

대체 사람들이 알고 있는 REST와 로이 필딩의 REST는 뭐가 다른걸까?!

## 따져보자
REST API는 REST 아키텍쳐 스타일을 따르는 API이다.
REST는 분산 하이퍼미디어 시스템(예: 웹)을 위한 아키텍쳐 스타일이다.
아키텍쳐 스타일이란 제약조건의 집합이다.
제약조건이란 REST를 구성하는 스타일로, 여섯가지가 있다.
- client server
- stateless
- cache
- uniform interface
- layered system
- code on demand (optional)

마지막 항목인 code on demand는 서버에서 코드를 클라이언트로 보내서 실행할 수 있어야한다는 뜻이다. 예를 들면 자바스크립트가 있다.
HTTP만 잘 따라도 위의 것을 잘 지킬 수 있지만 `uniform interface`라는것을 만족하기 어렵다고 한다.

## uniform interface란
- 리소스가 uri로 식별되어야 한다
- representation 전송을 통해서 리소스를 조작해야한다
- self-descriptive messages
- hypermedia as the engine of application state (HATEOAS)

밑의 두가지는 현재의 REST API라고 하는 모든것들이 지키지 못하고 있다고 한다. 하나씩 살펴보자.

### self-descriptive message
```javascript
GET / HTTP/1.1
```
위 코드는 루트를 얻어오는 단순한 get method를 표현한 코드이다. 하지만 목적지가 빠져있기 때문에 self-descriptive 하지 않다고 말할 수 있다.
REST스럽게 고치려면 아래처럼 수정해야한다.
```javascript
GET / HTTP/1.1
Host: www.example.org
```
어떤 문법으로 작성된것인지도 Content-Type 헤더 등으로 알려줘야한다. 그래야 파싱 가능해지고 문법 해석 가능해지기 때문이다.
예를 들면 아래처럼 작성해야 self-descriptive하다고 할 수 있다.
```javascript
HTTP/1.1 200 OK
Content-Type: application/json-patch+json

[ { "op": "remove", "path": "/a/b/c" }]
```
op, path가 무엇인지에 대해서도 작성해야 사실 완벽하다고 할 수 있다.
즉 메시지를 봤을때 이것만으로 해석이 가능해야 REST스럽다고 할 수 있다.

### HATEOAS
애플리케이션의 상태는 Hyperlink를 이용해 전이되어야한다.상태전이란?
예를 들면 index 페이지의 `목록보기`라는 버튼을 클릭했을 때 글 목록보기로 상태가 변경되는 것을 하이퍼링크를 통한 전이라고 할 수 있다.

HTML은 HATEOAS를 만족하는데 하이퍼링크(a태그)를 통해 다음 상태로 전이 가능하기 때문이다.
JSON으로 표현해도 만족 가능한데 Link헤더를 통해 하이퍼링크로 연결되어있는 다른 리소스로 연결 가능하기 때문이다.

### 그래서 uniform interface가 왜 필요할까?
서버와 클라이언트의 독립적 진화를 위해서 필요하다.
서버에서 uri가 바뀌는 등 변화가 일어나도 클라이언트에는 문제가 없어야한다.
REST를 만들게 된 계기는 'How do I improve HTTP without breaking the Web'이기 때문이다.


## 실제로 REST API는 잘 지켜지고 있는가?
잘 지켜지고 있는 예로는 `웹`이 있다.
- 웹 페이지를 변경했다고 웹 브라우저를 업데이트 할 필요 없다.
- 웹 브라우저를 업데이트 했다고 웹 페이지를 변경할 필요도 없다.
- HTTP 명세가 변경되어도 웹은 잘 동작한다.
- HTML 명세가 변경되어도 웹은 잘 동작한다.

HTML 5 첫 초안에서 권고안 나오는데 6년이 걸렸고 HTTP/1.1 명세 개정판 작업하는데 7년 걸렸다고 한다.
십여몇의 사람들이 문서를 다듬기만 하는데만 7년이 걸렸는데 그 이유는 하위 호환성을 깨트리면 안되기 때문이다.

### 상호운용성 (interoperability)에 대한 집착
오타인 Referer, 잘못 지은 이름인 charset (인코딩이라고 해야함) 등 모두 고치지 못한다.
또 HTTP 상태 코드 416을 포기했는데 만우절때 만들었던 416상태코드를 이미 다른 사람들이 사용해버려서이다.

### REST API
결과적으로 웹은 독립적으로 진화하고 있기 때문에 REST는 성공했다고 할 수 있다.
REST API는 REST 아키텍쳐 스타일을 따라야한다.
하지만 대부분 따르지 않고 있다. 
로이필딩은 제약조건을 다 지켜야만 한다고 말한다.

## 결론
REST가 쉽다는건 오해였다! REST도 어렵다.
원격 API가 꼭 REST API여야 하나? 그건 아니다.
로이필딩은 시스템 전체를 통제할 수 있다고 생각하거나, 진화에 관심이 없다면 REST에 대해 따지느라 시간을 낭비하지 말라고 한다. 

### 그럼 이제 어떻게 할까?
1. REST API를 구현하고 REST API라고 부른다
2. REST API 구현을 포기하고 HTTP API라고 부른다.
3. REST API가 아니지만 REST API라고 부른다. (로이필딩이 싫어합니다)

### 왜 API는 REST가 잘 안되나
웹은 잘되는데..무엇이 문제일까?
웹 페이지는 사람이 보지만 HTTP API는 기계가 해석한다는 점이 차이이다.
그러다 보니 미디어 타입이 다르다. 웹페이지는 HTML이고 HTTP API는 JSON이다.
즉 기계가 이해할 수 있는 포멧을 쓰게 된다.

문제의 원인이 미디어 타입이라는것을 찾았다!
HTML은 하이퍼링크가 되지만 JSON은 그런거 없다.
또 Self-descriptive 측면에서 사용해보면 HTML은 잘 명세되어있다 (각 태그마다 역할이 정해져있음). 하지만 JSON은 그런거 없다..! (중괄호, 대괄호 등은 정해져있지만 값들이 어떤 값을 가져야하는 지는 안 정해져있다)
즉 문법 해석은 가능하지만 의미를 해석하려면 별도로 문서(API 문서 등)가 필요하다는 뜻이다.

### self-descriptive와 HATEOAS가 독립적 진화에 어떻게 도움이 될까?
self descriptive는 확장 가능한 커뮤니케이션이 가능하게 한다.
서버나 클라이언트가 변경되더라도 언제나 해석이 가능하다. 서버가 링크를 바꾼다고 해도 클라이언트에서는 문제 없다. (링크는 동적으로 변경될 수 없다)

즉 독립적인 진화가 가능해진다.

### JSON을 REST API로 변경해보자!
`Media type`: 미디어 타입 문서를 작성해서 문서에 각각 값이 어떤 의미인지 정의하자.
그리고 IANA에 그 미디어 타입을 등록하면 된다. 하지만 몹시 번거롭다!

`Profile 이용`: 각각 값이 무엇인지 의미를 정의한 명세를 작성하자. link에 명세를 작성한 profile 걸면 된다. 하지만 클라이언트가 link를 이해해야한다.

`HATEOAS`: data에 다양한 방법, Link 또는 Location등으로 하이퍼링크를 표현하자. 
하지만 기존 API를 많이 고쳐야한다는 점이 단점이다. 

## 마무리
응준님의 발표자료를 통해 REST API가 무엇인지, 또 그 역사에 대해서도 알아봤다.
(역사라고 하기엔 현재 진행형인데다 REST라는 이름이 붙여진지는 20년 정도밖에 되지 않았지만..)
마지막으로 RESTful이 SOAP에 비해서 어떤점이 좋은지, 차이점이 무엇인지 정리하며 이 글을 끝내려고한다.
아래 부분은 [SOAP기반 / RESTful기반 웹서비스 비교](https://www.slideshare.net/seunghochoi4/soap-restful)를 참고했다.

SOAP
- HTTP, HTTPS, SMTP등을 통해 XML기반의 메시지를 컴퓨터 네트워크 상에서 교환하는 프로토콜
- (SOAP Envelope, SOAP Header, SOAP Body로 구성된 하나의 XML 문서로 표현됨)
- 복잡한 구조를 가지게 되어 HTTP상에서 전달되기 무겁고, 개발 난이도 높음.
- 서비스 지향 아키텍처


RESTful 웹 서비스
- HTTP 프로토콜로 데이터를 전달하는 프레임워크
- REST 아키텍처 스타일에 따라 정의되고 이용되는 서비스나 응용프로그램을 RESTful 웹 서비스라고 부른다.
- 자원 지향 아키텍처