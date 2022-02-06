---
title: Contentful로 Markdown 문서 작성하기
description: GraphQL에서 childMarkdownRemark 태그를 사용해보자
date: 2019-05-24
tags:
  - posts
layout: layouts/post.njk
---

GraphQL에 접속하면 사용가능한 태그를 볼 수 있는 Docs가 뜬다. 
내가 Contentful - ContentsModel 탭에서 만든 블럭의 이름이 가장 상단에 뜨고 그 이후에 사용 가능한 태그들이 뜨는데, 마크다운 형식의 문서를 편집해주는 MarkdownRemark 태그를 쓰기 위해서는 당연하겠지만 Contentful에서 마크다운 형식의 글을 써야한다.

문제는 아래의 두가지였다. 
> 1. Contentful에서 마크다운형식의 문서를 어떻게 작성하느냐
> 
> 2. 작성을 못한다면 적어도 불러오기는 어떻게 하는가
내 상식으로는 **컨텐츠풀의 용도** 라면 엄연히 마크다운 형식의 문서는 기본적으로 제공해야하는게 아닌가 했다.
내 상식은 이제 막 시작한 뉴비 프로그래머의 뇌를 가진, 사실상 일반인의 상식이기 때문에 그렇게 생각했을지도 모르겠다.

아무튼 내가 (체감상) 48시간이나 삽질을 했던 원인은 우선 가장 상단에 뜨는 태그의 이름이 내가 Contentful에서 설정한 블럭의 이름인줄 몰랐다(정해진 이름같은게 있을 줄 알았다). 또 내가 일상 블로그를 한다면 사실상 필요 없었을 Markdown 형식 문서가 필요했던 이유는 `<pre>` 태그 때문이였다. 그렇기 때문에 나는 로컬파일에서 불러올 수 있는 `MarkdownRemark`가 아닌 `ContentfulBlogPost`의 자식 태그에서 불러올 `childMarkdownRemark` 태그가 필요했다. 중간중간 수없이 많이 포기하고 싶은 욕구를 느꼈다. `MarkdownRemark` 태그를 쓰기 위해 필요한 plugin도 설치했는데 안되는거 붙잡고 있지말고 그냥 로컬폴더에 markdown 파일을 생성해서 Contentful 사용은 없었던 일로 하자 싶었다. 

그리고 '조금만 더 삽질하자'를 몇시간 더 하고 나니 반드시 해내서 문서로 작성해야겠다는 생각이 아주 강하게 들었다. 왜냐하면 적어도 내가 찾아볼땐 이것에 관한 문서가 없었기 때문이다.

사실 모든 정보가 이어져있는 문서가 없었을 뿐 부분적인 해결책은 있었기에 내가 결국 이 글을 적고 있는거겠지만. Contentful에 Markdown 익스텐션을 설치하고 컨텐츠 모델탭에 들어가서 컨텐츠의 형식을 롱텍스트로 바꾼 후 설치한 Markdown 익스텐션을 불러오고 graphQL에 들어가서 적용한 후 자바스크립트 파일에 적용 시키는 이 완벽한 순서가 담긴 문서가 나는 필요했던 것이다. 이제 내가 적을거니까 이제는 있겠지.

## Contentful에서 마크다운 형식으로 글을 쓸 수 있는 익스텐션 설치하기
[Contentful-Markdown-Editor-UI-Extension](https://github.com/TinkeringAround/Contentful-Markdown-Editor-UI-Extension)
에 들어가면 제목 그대로 Markdown Editor UI 익스텐션을 Contentful에 설치하는 방법이 나온다. 깃헙에 첨부되어있는 `index.html`파일안에 있는 코드를 복사해서 Contentful - Extension 탭에 가서 시키는대로 붙여넣기하면 끝. 

## 설치 후 마크다운 에디터 어디서 찾을 수 있죠?
이게 문제였다. 위 깃헙 사이트의 `README.md`에서 내게 제공하는 정보는 너무나 멋있게도 익스텐션 설치가 전부였다. 그래서 어떻게 사용해야하는데요? 
여기서부터 잘 보세요 여러분. 우선 Contentful Model탭에 들어가서 `add field`버튼을 누르면 사용가능한 새로운 field들의 리스트가 뜬다. 거기서 `text`아이콘을 클릭하면 Short Text와 Long Text를 선택할 수 있는 라디오 버튼이 있다. 그중에서 Long text를 선택하고 `Create and Configure` 을 클릭하면 configure창이 나타날것이다. `Appearance`탭에 들어가면 설치한 extension 리스트가 뜬다. 나는 Markdown 익스텐션만 설치 했기 때문에 선택 후 저장해주면 끝!
![Screen Shot 2019-08-30 at 13.43.31](//images.ctfassets.net/5xgpncj4c37m/3LzwTNP7ePFBgGLLuN5R95/fc2ffc513ad526aaa1550ae9463db666/Screen_Shot_2019-08-30_at_13.43.31.png)

## 이제 마크다운 에디터로 글을 작성해보자
블로그 글을 작성하기 위해 Content탭으로 들어가 Add Blog를 클릭하면 내가 설정한 마크다운 에디터의 이름이 적힌 탭이 생긴걸 확인할 수 있을것이다. 그럼 기본으로 제공하던 컨텐츠풀 텍스트의 탭과 다르게 마크다운 형식으로 글을 적으면 바로 적용된 모습을 확인할 수 있다. 
> 컨텐츠풀에서 제공하는 마크다운 에디터의 모습은 [Markdown, the way to write](https://www.contentful.com/blog/2015/09/22/markdown/)에서 확인할 수 있다.

## GraphQL코드 확인
이제 남은일은 GraphQL에 들어가서 우리가 그토록 원하던 `MarkdownRemark`태그를 써서 완성해주는 일뿐이다. 힘들게 얻은 이 코드를 복사해 우리의 .js파일에 붙여넣어주면 끝! 너무 즐겁다. 이 단계에서 희열을 느꼈다는 이야기가..


## 마무리
끝이다. 적어보니 간단한데 삽질을 왜 그리 오래 했을지 모를 일이다. 자려고 눕다가도 생각나고 밥 먹다가도 생각나서 체할뻔했던 일을 해결해서 기쁠 따름이다. 결론은 gatsby + contentful + netlify 조합은 최강이다! 다음글은 지킬에서 개츠비로 블로그를 마이그레이션한 대한 내용을 작성해볼까 한다. 
기대해주세요!