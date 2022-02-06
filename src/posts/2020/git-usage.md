---
title: git
description: 분산 버전 관리 시스템 git
date: 2020-04-14
tags:
  - Programming
layout: layouts/post.njk
------

블로그를 시작하고 가장 먼저 올린 글이 `git`에 관한 글이었다.
그도 그럴 수밖에 없었던 것이, 블로그 글을 올리기 위해서는 git을 사용해야 했기 때문이다.
그때에는 협업의 용도도 사용한 것도 아니었고 git이 무엇인지에 관한 공부 없이 사용했다.
그 당시 생각했던 '나는 언제쯤 협업을 하게 될까?' 에서 언제쯤은 정확히 1년 뒤인 지금이 되었고, 인제야 git에 대해서 알아보고자 한다. 이번 글에서는 git - 분산 버전 관리 시스템이 무엇인지, 그리고 몇 가지 내가 헷갈리는 개념에 관해서 이야기 할 예정이다.

## 분산 버전 관리 시스템
git은 DVCS (Distributed Version Control Systems)이다. 번역을 하자면 분산 버전 관리 시스템 정도가 되겠다. 깃은 저장소를 히스토리와 더불어 전부 복제한다. 그렇기 때문에 서버에 문제가 생기더라도 이 복제물로 다시 작업을 시작할 수 있다. 분산 버전 관리 시스템은 git 이외에도 `Mecurial`, `Bazaar`, `Darcs` 등이 있다.

## git의 역사
거창하게 역사라고 할 것까지는 없지만, 때는 2002년으로 거슬러 올라간다. 나는 무엇이든지 간에 시발점을 찾아보는 것을 좋아하는데, 프로그래밍 (또는 소프트웨어)와 관련된 것을 찾아볼 때면 그리 오래되지 않았음에 항상 놀라곤 한다. 그들과 동시대를 살아가고 있음은 참 신기한 일이다!

그래서 역사에 대해서 말해보자면, 200년 Linux 커널은 `BitKeeper`라고 불리는 상용 DVCS를 사용하기 시작했다. 그리고 2005년 Linux 커널과 BitKipper은 사용권 문제로 싸우게 되는데..! 에잇 내가 직접 만들어 버리고 말지 하며 리누스가 직접 DVCS를 만들기 시작한다. 그것이 바로 오늘날의 `git`이다. 소문에 의하면 리누스 토발즈가 몇 시간 만에 깃 최초 버전을 만들었다고 한다.. 무서운 사람이다.

## git pull
### git fetch 
- 서버에는 존재하지만, 로컬에는 아직 없는 데이터를 받아와서 저장
- 서버로부터 데이터를 가져와서 저장해두고 사용자가 `Merge` 하도록 준비만 해둔다

### git pull
- `git fetch` 명령을 실행하고 나서 자동으로 `git merge` 명령을 수행한다
- 서버로부터 데이터를 가져와서 현재 로컬 브랜치와 서버의 추적 브랜치를 머지한다

> fetch 와 merge 명령을 명시적으로 사용하는 것이 pull 명령으로 한번에 두 작업을 하는 것보다 낫다

## Rebase 하기
Rebase는 한 브랜치에서 다른 브랜치로 합치는 방법 중 하나이다 (다른 하나는 `merge`). Rebase를 하게 되면 브랜치의 공통 조상이 되는 base를 다른 브랜치의 커밋 지점으로 바꾸어진다.

![rebase](https://wac-cdn.atlassian.com/dam/jcr:e4a40899-636b-4988-9774-eaa8a440575b/02.svg?cdnVersion=989)
위의 이미지를 살펴보자. 우리는 master 브랜치의 마지막 커밋에 feature의 변경 사항이 일어난 것 처럼 보이게 하고 싶다.그럴 때 바로 `rebase`를 사용하면 된다. rebase는 `feature의 base`를 master 브랜치의 가장 마지막 커밋으로 설정해준다. 

> 이미지 출처: [atlassian- git rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)

[git 공식 문서](https://git-scm.com/docs/git-rebase)를 살펴보면 "By default, a rebase will simply drop merge commits from the todo list, and put the rebased commits into a single, linear branch." 라는 말을 찾아볼 수 있다. 기본적으로 Rebase는 머지커밋을 실행 목록에서 없앤다. 그리고 Rebase 한 브랜치의 Log를 살펴보면 히스토리가 선형임을 확인할 수 있다 (병렬로 일을 진행했음에도 선형 처럼 보인다).

## git 설정
git 설정에 대해서 알아보자. 우선 깃 설저은 `git config` 라는 명령으로 내용을 확인하고 변경한다. `/etc/gitconfig` 파일에서는 시스템의 모든 사용자와 저장소에 적용되는 설정을 확인할 수 있다. `~/.gitconfig`, `~/.config/git/config` 파일에서는 특정 사용자 (현재 사용자)에게만 적용되는 설정을 확인할 수 있다. `.git/config` 파일은 Git 디렉토리에 있는 파일/ 특정 저장소 (현재 작업중인 프로젝트)에만 적용된다.

### pull 시에 머지 커밋이 남지 않도록 하기
```bash
git pull --rebase

// or
git fetch
git rebase teamone/master
```

`git pull` 명령을 실행할 때 기본적으로 `--rebase`옵션이 적용되도록 `pull.rebase` 설정을 추가할 수 있다

```bash
git config --global pull.rebase true
```

## git에 관련된 꿀팁!
사수님께서 알려주신 git 사용 꿀팁에 대해서도 언급을 해볼까 한다! 내가 까먹지 않고 잘 사용할 수 있도록 기록하는 이유가 크다.

### 파일 상태를 Unstage로 변경하기
- `git add *` 를 이미 실행해 버린 후 staging area에서 파일을 꺼낼 때 사용한다

```bash
git reset HEAD {n}
```

### 커밋 수정하기
지난 커밋을 수정하고 `rebase`하는 일은 지양해야한다 (일단 push했을 때 이야기). 하지만 커밋 메시지를 수정하는 용도로 쓸 때에는 유용하다.

```bash
git rebase -i HEAD~{n}
```

### 여러개의 커밋 로그를 하나로 묶기 `git squash`
가능하면 이미 `push`하지 않은 작업만 `squash` 하는것이 좋다. `interactive rebase`를 하는데 필요한 명령어 중 하나이다.

### 일단 push 했는데 정말 고쳐야할 때
지난 커밋 기록은 그대로 둔 채로 해당 커밋을 되돌릴 수 있다.

```bash
git revert {n}
```

### 하던 작업을 임시로 저장해두고 싶을 때
뭔가 막 변경중인데 변경중인 것을 보관한채로 커밋을 풀하거나 푸시해야할 때 쓴다.

```bash
git stash
```

### 그 외
- 일단 푸시한 커밋은 절대 `rebase` 와 `force-push` 하지 않는다.
- 언제나 커밋/푸시 하기 전에 `pull` 부터 한다.
- 깃은 `hash`로 기록이 남기 때문에 `history`가 약간만 달라져도 그 커밋 이후의 모든 커밋 `hash`가 바뀐다. (주의)

## wrap-up
혼자 룰루랄라 git을 사용하던 때와 협업을 하면서 사용하는 git은 완전히 다른 느낌이다. 내 명령어 하나가 프로젝트에 끼칠 영향을 생각하면 `git`이라는 도구를 대하는 마음가짐도 달라진다. 이제 막 git으로 협업을 시작해서 아직 공부해야 할 것들이 많이 남았다. 어쨌든 깃은 참 우리네 인생을 편하게 만들어주는 멋진 도구임이 틀림없다. 깃을 능수능란하게 다루게 될 그 날까지..! 화이팅이다 나 자신.