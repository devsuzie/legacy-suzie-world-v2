---
title: Gatsby블로그에 Contentful글을 Netlify로 배포하기
description: gatsby + contentful + netlify의 만남은 아름답다..
date: 2019-05-27
tags:
  - posts
layout: layouts/post.njk
---

Front End 개발자가 되겠어! 라는 멋진 마음을 먹고 Jekyll 블로그를 만든지 두달 후.. Gatsby가 그렇게 핫하다는 소문이 트위터에 떠돌아다니기 시작했다.. 나 김수지 팔로워 6명.. 가만히 있을 수 없었다. 블로그 글도 세개밖에 없어서 마이그레이션이라고 할것도 없지만 마이그레이션한 후기! 

개발 새발 아무것도 모르는 나에게 용기를 준것은 Gatsby의 멋있고 아름다운 튜토리얼 이였다. 튜토리얼은 [Gatsby.js Tutorials](https://www.gatsbyjs.org/tutorial/)에서 확인할 수 있다. 한눈에 들어오는 목차, 포기하지 않고 따라할 수 있게 중간중간 용기를 주는 멘트까지. 나는 할 수있다! 그리고 했다. 내가 튜토리얼을 따라하는 모습은 [I Tried Following A Gatsby Tutorial](https://www.youtube.com/watch?v=BNYBTADGaJE&t=7s)에서 확인할 수 있다. 내가 개츠비로 블로그를 옮기면서 특히 감명깊었던 부분은 `contentful`과 `netlify`를 사용할때였다. 이 부분에 대해서 말해보자.

## Netlify에 Gatsby 웹사이트 연결하기
Gatsby tutorial을 보고 블로그 웹페이지를 만드는데 성공했다면 Netfliy에 들어가서 계정을 만들고 개츠비 블로그의 깃헙 레포를 연결시키자. 그리고 `Deploy site`를 눌러 사이트 배포! 첫번째 단계인 사이트 배포를 통과했으면 다음은 커스텀 도메인을 설정할 차례이다. 원하는 도메인이 있다면 이곳에서 설정하자.

## Contentful로 블로그 글 작성하기
 이제 [Contentful](https://www.contentful.com/)을 사용해 블로그 글을 작성해보자. Contentful 계정을 생성하고 `Create Space`로 space를 생성했다면 Content model탭에서 `Add content type`버튼을 눌러 나의 content type을 만들자! 그리고 나서 안에 들어갈 field들을 채워주면된다. 나의 경우에는 Mark down형식으로 글을 쓸 수 있는 Text 필드가 필요해 고생을 좀 했다. 나의 고생담은 [Contentful로 Markdown 문서 작성하기](https://www.suzie.world/blog/contentful-markdown) 이곳에서 확인할 수 있다. 

![pic1](//images.ctfassets.net/5xgpncj4c37m/7t4G03ScZsueRCq7LMfEz5/410eb6c0829ae7bb4d75619087dfe9e8/pic1.png)

## Netlify로 Contentful 블로그 글 배포하기
Contentful에서 글 작성 후 `Publish` 버튼을 클릭하자. 그리고 Netflify의 Deploy Page에서 Trigger deploy 버튼을 눌러서 뜨는 `Deploy site`를 클릭하면 Deploy log가 뜨면서 발행중인것을 알려줄것이다. 여기서 Netlify의 좋은점이 나타난다! 이전에 Jekyll에서 블로그 글을 깃을 통해 발행할때에는 언제 발행이 완료될지 모르는채 기다리다 한참 뒤 웹사이트를 새로고침하고는 했다. 하지만 이제 그런 걱정은 그만.. Deploy log에서 지금 발행을 위해서 무엇을 하고있는지 실시간으로 알려줄뿐 아니라 아이콘으로 `Building`중임을 알려준다. 성공적으로 완료되면 `Published` 아이콘을 볼 수 있다.

![pic2](//images.ctfassets.net/5xgpncj4c37m/4WOcnN4HYbnD8PYL2mt76a/5ee322b32df53714c2f0654c74bb6577/pic2.png)

## 마무리
끝이다. 너무 간단하고 빠르고 쉬워서 눈물이 나려고한다.. 마지막으로 웹사이트를 만들면서 느꼈던 Gatsby, Netlify, Contentful 각각의 좋은점을 써보겠다. `Gatsby`는 웹사이트를 만들면서 바로 바로 바뀌는 모습을 실시간으로 확인할 수 있어서 좋았다. Plug in 설치도 홈페이지에 자세히 설명되어있어서 편리했다. 문서가 잘 작성 되어있어서 막히는게 생기면 바로 바로 찾아볼 수 있다는 점이 나에게는 큰 도움이 됐다. `Netlify`는 호스팅이 빠르고 쉽다는것이 가장 큰 장점이다. 언제 배포될까 하며 기다리지 않아도 되고 터미널도 필요없이 버튼 하나 누르면 된다니! `Contentful`은 마크다운 에디터가 contentful 자체에서 제공하는 UI로 만들어져있어서 미디어나 링크등 다른 정보를 추가할때 편리했다. Graph QL로 원하는 field를 만들고 원하는대로 블로그를 커스텀할 수 있다는 것이 정말 아름다운것..

나의 마지막 총평으로 이 글을 마무리 하겠다. Jekyll로 웹사이트를 만들때는 이미 한국에서 지킬로 블로그를 만든 분들도 많이 계셨고 포크 뜰 깃헙 레포지토리도 너무 많았다. 결국 내 힘으로 한건 스타일 변경 정도가 전부였겠다. 그런데 개츠비로 블로그를 만들면서 빌드부터 런치까지 다 하니 감회가 새롭다. 내가 신봉하는 에자일 기법으로 만든거라 아직은 버전1이지만 점점더 발전할것이다! 아자아자.