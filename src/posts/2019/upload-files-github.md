---
title: Git을 이용해 Github에 파일 올리기
description: 로컬에 있는 파일을 github 저장소에 어떻게 올리지?
date: 2019-04-02
tags:
  - posts
layout: layouts/post.njk
---

지킬을 이용하여 블로그를 만들고 나서, 어떻게 파일을 깃헙에 올릴 것인지에 대한 문제가 생겼다.
참고한 블로그에서는 `source tree` 라는 Git GUI를 이용해 업로드 하는 형식을 소개했는데, 여기서는 터미널을 이용해 올리는 방법을 소개할 예정이다.

> Jekyll 설치는 [비개발자에게 진입 장벽이 높은 Jekyll 설치하기](http://jihyeleee.com/blog/designer-can-make-jekyll-blog/) 를 참고했다.

## git config

`configuration` 에서 따온 config다.
git을 설치 후 사용 환경을 구성해주는 역할인데 commit시 사용하게 되는 사용자 정보를 담고 있다.
협업 시 이 정보를 보고 누가 커밋 메시지를 남겼는지 알 수 있다. (난 언제쯤 협업을 하게 될까 ..)
github에서 사용하는 user name 과 email 주소를 넣으면 된다.

``` bash
git config --global user.name <username>
git config --global user.email <mailaddress>
```

> 이때 `--global` 옵션을 사용하지 않으면 해당 저장소만 유용한 설정이 되므로 꼭 붙여줍시다.

## git init

먼저 프로젝트의 디렉토리로 이동 한다.

```bash
cd <file directory>
```

> cd 타이핑 후 원하는 디렉토리의 파일을 끌어 당겨 오자. 자동으로 절대 경로가 입력된다.

이제 새로운 git 저장소를 해당 폴더에 만들자.

```bash
git init
```

## git add

추가 또는 수정한 파일들을 준비영역 즉 staging area로 add 해준다.
`add --all` 또는 `add .` 은 새로운 파일들 전부를 add 해주는 명령어다.

```bash
git add .
```

## git commit

이제 변경내용을 확정하기 위해 커밋 메시지와 함께 커밋해주자.
커밋 메시지는 간단하지만 변경사항이 무엇인지 누구나 알아볼 수 있도록 써야하는데 어떻게 그렇게 쓰지?
여기까지 하면 변경된 파일이 HEAD 에는 반영 되었지만, MASTER 즉 원격 저장소에는 아직 반영되지 않았다.

```bash
git commit -m ""
```

> " 와 " 사이에 커밋 메시지를 작성하면 된다.

## git remote

원격 저장소와 로컬의 repository를 연결시켜주자. 자신의 계정 깃헙 repository에서 `clone or download` 버튼을 누르면 복사 가능한 url 주소가 나타난다. 그 주소가 바로 우리가 원하는 원격 저장소의 주소다.

```bash
git remote add origin <>
```

> < 과 > 사이에 복사한 url 주소를 붙여넣기 하자.

## git push

원격 저장소와 로컬도 연결했으니 이제 진짜로 원격저장소에 업로드 해보자.

```bash
git push origin master
```

## 마무리

깃헙 블로그 가지고 있는 사람들 멋있어 보여서 나도 가지고 싶었을 뿐인데 의도치 않게 `git` 과 `gulp`에 대해서 배우게 되었다. Jekyll 블로그 솔루션을 추천해준 지혜언니에게 감사의 말을 전한다. 다음에는 gulp에 대한 이야기를 해볼까 한다. 기대해주세요 !
