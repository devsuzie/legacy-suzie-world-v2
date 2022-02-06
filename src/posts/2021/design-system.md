---
title: 디자인 시스템
description: 더 큰 문제 해결을 위한 디자인 시스템
date: 2021-08-22
tags:
  - Design
layout: layouts/post.njk
------

디자인 시스템. IT 회사를 다니는 사람이라면 한번쯤 들어봤을거다. `디자인 시스템` 이라는 워딩을 처음 마주한 나에게는 이것이 어떤 의미인지 크게 와닿지 않았다. 회사를 다니며 디자인 시스템을 사용하면서 어떤 의미인지 점차 알게 되었다. 현재 나에게 디자인 시스템의 의미란 하나의 규약이다. 한 회사의 제품 개발을 위한 `Ground Rule`이라고도 할 수 있겠다. 하나의 앱에서도 수많은 기능이 탄생하고 사라진다. 이러한 과정에서 여러명의 개발자와 디자이너들이 각기 다른 기능을 만들면서도 공통된 제품으로 완성되도록 하는것. 이러한 역할을 하는것이 디자인 시스템이 아닐까 한다. 이번 글에서는 디자인 시스템이 무엇인가에 대해 이야기 하고 대표적인 디자인 시스템 예시들을 살펴보고자 한다. 

## 디자인 시스템이란?

디자인 시스템이란 무엇일까? 하나의 브랜드를 만들기 위한 디자인 구성 요소들의 집합체라고 생각한다. 글에서는 앞으로 구성 요소를 `컴포넌트`라 지칭하겠다. 이러한 컴포넌트들을 합쳐서 하나의 커다란 또다른 컴포넌트를 만들 수 있을것이다. 화면을 개발하면서 버튼 컴포넌트를 만들고, 버튼 컴포넌트를 다이얼로그 컴포넌트에 사용하는 것과 비슷한 맥락이다. 디자인 시스템에서 이러한 컴포넌트에는 가이드 라인이 포함될 수 있고, 색상이나 폰트 스타일의 이름도 포함될 수 있다. 예를 들면 이런것이다. 자주 사용하는 색상의 HEX 값이 `#13BD7E` 라고 해보자. 우리는 항상 이 색상을 지칭할때마다 `그 초록색` 이라던가 `#13BD7E` 라고 부를 순 없을거다. 전자의 경우에는 듣는 사람마다 다른 초록색을 생각할 경우의 수가 있고, 후자의 경우에는 `#` 를 입력하는 순간부터 오타와의 전쟁이 시작된다. 해결책은 무엇일까? 이 색상에 `GREEN-100` 이라는 이름을 붙여주는것이다. (실제로 회사에서 사용하는 이름을 가져와서 예시로 사용했다.) 이런 내용들을 디자인 시스템에서는 포함하고 있다. 

이런한 규약은 코드 작성 단계에서도 이어지는데, 마치 서버 명세인 IDL을 import하여 타입에 사용하듯 이러한 이름들을 Global Style 설정 파일에 저장해두고 사용할 수 있다. 이렇게 하였을때의 장점은 다른 사람의 코드를 리뷰할때 코드만 보아도 이게 어떤 화면인지 그려질것이라는 것이다. 또한 그라운드 룰이 변경되었을 경우 간단히 해당 파일의 이름에 부여된 값만 수정해주면 되니 전반적인 코드를 살펴보아야 할 일도 없을것이다. 

## 구글의 디자인 시스템: Material Design

디자인 시스템 하면 Google의 [Material Design](https://material.io/)이 대표적으로 떠오른다. 구글은 메테리얼 디자인에 대해 아래와 같이 소개한다.

> Material Design is a system for building bold and beautiful digital products. By uniting style, branding, interaction, and motion under a consistent set of principles and components, product teams can realize their greatest design potential.

그들은 메테리얼 디자인을 통해 스타일, 브랜딩, 인터렉션과 모션이 하나로 모여진 규칙과 컴포넌트를 사용할 수 있다고 말한다. 메테리얼 디자인은 OS 디자인 시스템이기 때문에 각각의 OS에서 어떻게 디자인 시스템을 적용할 수 있는지 알려준다. 

![image](https://user-images.githubusercontent.com/40863240/130814360-0ee9e26e-91a4-40d3-9db5-87fd6398194e.png)

[Material Studies](https://material.io/design/material-studies/about-our-material-studies.html) 섹션에서는 이러한 디자인 시스템을 적용한 웹앱들을 소개한다. 레이아웃과 색상, 타이포, 아이콘등에 대한 상세한 구분을 통해 설명하고 있다. 이 구글의 디자인 시스템은 많은 사람들이 따라갈 수 있는 길을 열어주었다. 실제로 내가 디자인에 처음 관심을 가지게 되었을때 디자이너분께서 메테리얼 디자인을 꼭 읽어보기를 추천해주셨다. 구글의 디자인 시스템을 통해 수많은 디자인 팀이 이러한 시스템에 더욱더 관심을 가지게 되었을것이라고 생각한다. 

## 애플의 디자인 시스템: Human Interface Guidlines

Apple의 디자인 시스템인 [Human Interface Guidelines](https://developer.apple.com/design/) 또한 OS 디자인 시스템이다. 아래와 같이 macOS, iOS, watchOS, tvOS 등 서로 다른 OS에 대한 가이드라인을 소개하는 것을 확인할 수 있다. 

![apple](https://user-images.githubusercontent.com/40863240/130814601-5a3275ad-58d1-4949-8b0e-e047fc47c273.png)

> Get in-depth information and UI resources for designing great apps that integrate seamlessly with Apple platforms.

애플은 디자인 시스템에 대해 위와 같이 소개하고 있다. 애플 플랫폼과 완벽하게 어우러지는 앱을 디자인하기 위한 `UI 리소스`와 `정보들`. 각 OS에 맞추어 개발을 할 때 사용할 수 있는 수많은 리소스와 다운로드 가능한 탬플릿들을 제공한다. 애플의 디자인 철학에 걸맞게 심플함이 강조되어있음을 디자인 시스템을 통해서도 살펴볼 수 있다. 

## 마이크로 소프트의 디자인 시스템: Fluent Design System

마이크로 소프트의 [Fluent Design System](https://www.microsoft.com/design/fluent/#/) 에 대한 설명을 살펴보자.

> Fluent is an open-source, cross-platform design system that gives designers and developers the frameworks they need to create engaging product experiences—accessibility, internationalization, and performance included.

Fluent는 디자이너와 개발자들에게 제품 경험을 위한 접근성, 국제화 및 성능을 제공해주는 오픈 소스이자 크로스 플랫폼 디자인 시스템이라고 한다. 크로스 플랫폼, OS 디자인 시스템이기 때문에 아래 사진처럼 OS에 맞추어진 다양한 디자인 시스템을 제공한다. 

![ms](https://user-images.githubusercontent.com/40863240/130814692-1d898ef4-4e94-4a42-8f8b-4ab766f2763a.png)

Microsoft 디자인 시스템은 쉽고 빠르게 디자인 모델을 만들 수 있도록 지원한다. 실제로 [깃헙 레포](https://www.notion.so/084ad415a2324b2aab5963e724891638)에서 해당 코드를 확인할 수 있으며 `Fluent UI React project` 를 생성하기 위한 명령어도 각 OS별로 지원하고 있다. CRA 탬플릿 앱을 통해 Fluent 리액트 프로젝트를 생성할 수 있는 명령어 또한 제공하고 있다. 

```bash
# Option 1: using npx/npm
npx create-react-app my-app --template @fluentui/cra-template
cd my-app
npm start

# Option 2: using yarn
yarn create-react-app my-app --template @fluentui/cra-template
cd my-app
yarn start
```

## 에어비앤비 디자인 시스템: DLS

에어비앤비의 디자인 시스템인 [Design Language System](https://airbnb.design/the-way-we-build/) 을 이렇게 소개하고 있다. 

> The DLS is a collection of components defined by shared principles and patterns. This allows for rapid iteration using a shared vocabulary across design, engineering, and other disciplines. The structure of the DLS is simple and coherent, easing communication across teams.

DLS는 원칙과 패턴으로 정의된 컴포넌트의 모음이고, 이를 사용해 디자인과 개발 단계에서 빠르게 제품 개발을 반복할 수 있다고 한다. 또한 DLS의 구조는 간단하고 일관성이 있어 팀 간의 의사 소통에 도움을 준다고 한다. 이 짧은 문장에서 에어비앤비 디자인 팀의 디자인 철학에 대해 살펴볼 수 있었다. 그들의 철학은 이어지는 이야기에서도 잘 나타는데, 제품을 만드는 방법을 구축하기 전까지는 제품을 구축할 수 없다고 말한다. 

![DLS](https://user-images.githubusercontent.com/40863240/130814769-862f285e-ff07-4ff3-9c77-6e7be2423bb0.png)

에어비앤비 팀은 디자인 시스템을 구축하면서 디자이너와 개발자가 공통 언어로 협업할 수 있는 즐거움, 디자이너와 개발자 각각 스타일 유형, 코드 네이밍이 아닌 로직과 설계에 집중할 수 있는 즐거움을 경험했다고 한다. 더 나은 디자인 시스템을 만들어 나가면서 더 큰 문제를 해결하기 위한 도구라는 문구도 인상적이다. 에어비앤비의 디자인 시스템 툴을 자세히 살펴볼 수 없는것이 아쉽지만 후기와 사례 연구에 대해서 블로그를 통해 알려주기 때문에 해당 글을 에어비엔비 디자인 블로그에서 읽어보는 것을 추천한다. 

## 마무리

각 브랜드가 설명하는 디자인 시스템의 정의를 살펴보면 브랜드의 디자인 철학이 담겨있음을 알게된다. 글에서는 대표적인 해외 기업들의 디자인 시스템을 소개했지만 국내에서도 디자인 시스템을 잘 운영하고 있는 곳이 많다. 뱅크샐러드의 [Banksalad Product Language를 소개합니다](https://blog.banksalad.com/tech/banksalad-product-language-ios/) 글과 토스의 [서비스 개편, 토스팀 디자이너 4명에게 직접 들어봤습니다](https://blog.toss.im/article/toss-designer-interview) 글을 추천한다. 또한 당근마켓에서는 [당근마켓의 디자인 시스템 모노 레포](https://github.com/daangn/karrot-ui)를 깃헙에 공개하고 있다. 

백오피스 작업을 하다가 처음으로 웹앱 작업을 하게 되면서 디자인 시스템을 사용했던 첫 날이 떠오른다. 디자인 시스템을 사용하면 이렇게 화면이 금새 만들어질 수 있구나! 하고 깜짝 놀라는 경험을 했다. 물론 디자인 시스템은 계속해서 개발되어야 한다. 이 과정에서 디자인 팀과 개발팀에서의 끝없는 협업이 발생한다. 그 과정은 쉽지도 않고 짧지도 않지만 그러한 과정을 통해 "더 큰" 문제를 쉽게 해결할 수 있으니 그것을 사용하는 개발자 입장에서는 그저 감사할 따름이다. 

에어비앤비 디자인팀의 디자인 시스템을 만드는 목적에 대한 문장으로 이번 글을 마무리하려고 한다. "우리는 차이가 모호하고, 논리와 디자인이 진정으로 공존하며, 협업이 고통스럽지만 영감을 주는 세상을 만들고 있습니다."