---
title: Subdomain with Netlify
description: Netlify를 사용하여 서브도메인으로 Github 리포지토리를 호스팅하는 방법
date: 2019-09-20
tags:
  - posts
layout: layouts/post.njk
------

나는 [www.suzie.world](https://www.suzie.world/) 도메인으로 포트폴리오 사이트를 Netlify를 사용해 호스팅했다. 문제는 내가 작업한 프로젝트를 서브 도메인에 호스팅하고 싶어 하면서 발생했다. Netlify는 하나의 깃 리포지토리로부터 호스팅 되어있기 때문이다. 당연히 내가 작업한 프로젝트는 다른 깃 리포지토리에 저장되어 있고 말이다. 이번 글에서는 내가 작업한 프로젝트를 `project.suzie.world` 이름으로 호스트 하는 방법에 대해서 알아볼 것이다. 사실 두 달 전에 분명! 했었는데 누가 내 기억을 삭제해버린 것 마냥 기억이 나지 않아 나를 위해 적는 글~!

## Connect to Git provider
메인 도메인이 설정되어 있다면 [app.netlify](https://app.netlify.com/)에 접속했을 때 아래의 사진과 같은 화면이 나타날 것이다. 새로운 서브 도메인 사이트를 만들기 위해 첫 번째로 오른쪽 상단에 있는 `New site from git`을 클릭하자. 클릭 후 `Create a new site` 페이지가 나타나면 첫 번째로 깃 provider을 선택하면 된다. Github, GitLab,Bitbucket 세 개의 깃 provider가 있는데 나는 깃헙 리포지토리에서 가져올 것이기 때문에 `Github`선택했다.

![1](//images.ctfassets.net/5xgpncj4c37m/7fGOl2E4keGxjfJjT7uzcJ/2fe2b626e6afd6cfc75f3b7f1ef18a3e/1.png)

## Pick a Repository
두 번째 단계는 리포지토리를 선택하는 것이다. 내가 가져오고자 하는 리포지토리가 선택지에 없는 경우에는 하단의 `Configure the Netlify app on GitHub`문구를 클릭하자. 링크와 연결된 깃헙에 로그인하고, 불러올 깃헙 리포지토리를 선택하면 된다. 다시 Netlify로 돌아오면 선택한 깃헙 리포지토리가 불러와 져 있는 것을 볼 수 있을 것이다.

![2](//images.ctfassets.net/5xgpncj4c37m/6XgI1L0FJl6oaFGOQGJeKK/80f1fb8a7d029d46af190b7d1ce4ad15/2.png)

## Build options, and deploy!
불러온 리포지토리를 선택하면 세 번째이자 마지막 단계인 `Build options, and deploy!` 탭으로 넘어간다. 말 그대로 옵션을 설정하고 사이트를 디플로이 하는 탭이다. 옵션 설정 먼저 살펴보자. `Basic build settings`은 에서 Build command란에는 실행할 때 필요할 명령어를, Public directory에는 html, css, JS 파일이 들어있는 퍼블릭 폴더명을 입력해주면 된다. 옵션 설정까지 완료했다면 `Deploy site` 클릭!

![5](//images.ctfassets.net/5xgpncj4c37m/3fBkvIqj6QIGbrVOA6VlUN/39628a8a99c28c7a7f292f89fa3d5fac/5.png)

## Set up a custom domain
사이트를 디플로이 하고 나면 사이트의 상세 페이지가 뜬다. `.netlify.com`으로 끝나는 netlify에서 제공하는 도메인으로 들어가서 확인할 수 있다. 우리가 하고 싶은 일은 서브 도메인에 등록하는 것이니 두 번째 단계인 `set up a custom domain`을 클릭하자

![6](//images.ctfassets.net/5xgpncj4c37m/2egWoPMjvFGseM9D9ZZ3qu/7492620647ccedfcc85b882188688266/6.png)

## Add a custom domain to your site
자신이 등록하고 싶은 커스텀 도메인의 이름을 입력하면 끝이다. 나는 todo 앱을 만들었기 때문에 `todo.suzie.world`로 도메인 이름을 작성했다.

![7](//images.ctfassets.net/5xgpncj4c37m/2MLZbiEPA9xbyOMKUY8j8v/48cf26723593456c12afad508bf48568/7.png)

> 디플로이한 사이트가 서브 도메인에 등록이 되었는지 확인해보자. [todo.suzie.world](https://todo.suzie.world) 잘 등록 되었다 !
