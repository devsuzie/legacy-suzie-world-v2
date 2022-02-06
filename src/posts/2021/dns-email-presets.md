---
title: 도메인과 이메일의 연결고리
description: 커스텀 도메인에 이메일을 연결하고 Gmail에서 확인하는 방법
date: 2021-07-25
tags:
  - Programming
layout: layouts/post.njk
------

사이드 프로젝트 작업을 하면서 도메인을 새로 생성했다. 사이트를 만들고 ABOUT 페이지를 작업하면서 연락할 수 있는 이메일을 넣으려다가 고민이 생겼다. 지금 블로그로 사용하고 있는 개인 사이트의 경우 간단히 gmail을 사용하고 있는데, 새로 생성한 도메인을 사용해서 이메일을 만들면 좋겠다 싶었기 때문이다. 

여기에는 조건이 있었는데, 구글을 활용해서 메일을 확인할 수 있으면 좋겠다는 것이었다. 그러자 간단히 떠오르는 생각은 [Gsuite](https://workspace.google.com/intl/en_ie/features/?utm_source=google&utm_medium=cpc&utm_campaign=emea-cz-all-en-dr-bkws-all-lv-trial-e-t4-1010042&utm_content=text-ad-none-none-DEV_c-CRE_526908326583-ADGP_Desk%2BTab%20%7C%20BKWS%20-%20EXA%20%7C%20Txt%20~%20G%20Suite%20~%20General-KWID_43700016122612449-kwd-7564271891-userloc_1009871&utm_term=KW_g%20suite-ST_g%20suite&ds_rl=1259922&gclid=Cj0KCQjw9O6HBhCrARIsADx5qCRz4kFxqgr46jbsgbjS1figY6Ztz-BXxwYg-eAkjfl_3VimH55Ph8EaAp2EEALw_wcB&gclsrc=aw.ds)를 활용해볼까? 였다. 하지만 구글은 호락호락하게 무료 플랜을 내어주지 않는다! (DNS를 구글에 등록하면 된다고 하지만 나는 DNS를 이전할 생각이 없다!) 1인 사용에 이메일 양이 많지 않은 나에게는 유료 플랜이 매력적이지 않았다. 사이드 프로젝트 작업의 DNS는 [Vercel](https://vercel.com/)를 이용하고 있어서 Vercel에서 제공하고 있는 것이 없을지 확인해보았는데 역시나 없었다.  

## 어떤 이메일 포워딩 서비스를 사용할까?

Vercel docs의 [DNS Email Presets](https://vercel.com/docs/custom-domains#dns-email-presets) 를 살펴보면 Vercel에 추가한 도메인에 이메일을 연동하려면 `MX` 타입의 DNS 레코드를 추가해야 한다고 되어있다. DNS 레코드를 설정할 수 있는 `Domains` 탭에 들어가보면 `Add Email Prestes` 버튼을 볼 수 있다. 꽤나 많은 선택지가 있는데 이 중에서 선택하면 된단 말이지...

![https://user-images.githubusercontent.com/40863240/126888179-e07e4848-07e1-41c2-a54e-a6bd7c5bfc27.png](https://user-images.githubusercontent.com/40863240/126888179-e07e4848-07e1-41c2-a54e-a6bd7c5bfc27.png)

많은 목록 중 나는 [ImprovMx](https://improvmx.com) 를 사용하기로 결정했다. 첨부 파일을 보내거나 전달 받지 않아도 되고, 하나의 도메인에만 연결할 생각이기 때문에 ImprovMx에서 제공하는 무료 플랜이 나에게 적합하다고 생각했다. 나중에 사이드 프로젝트가 MAU 100만 찍으면 유료 플랜 쓰겠습니다! (당당) [zoho](https://www.zoho.com/mail/zohomail-pricing.html?src=hd)는 한달에 1달러에 여러 도메인에 이메일 호스팅을 제공하고 여러 추가적인 기능을 제공하고 있어서 나중에 개인 블로그를 마이그레이션할 때 zoho를 사용할까 생각중이다. 

![https://user-images.githubusercontent.com/40863240/126888290-7c6f4295-7d4e-4b9b-9c05-7ae021940952.png](https://user-images.githubusercontent.com/40863240/126888290-7c6f4295-7d4e-4b9b-9c05-7ae021940952.png)

[ImprovMx](https://improvmx.com) 사이트에 들어가면 바로 이메일 알리아스를 등록할 수 있는 화면이 보인다. 왼쪽 란에는 연결하고자 하는 도메인의 주소를, 오른쪽 란에는 해당 도메인 주소로 전달 받은 이메일을 확인하고 싶은 주소를 작성하면 된다. 

![https://user-images.githubusercontent.com/40863240/126888349-12e03bec-3448-4bdf-bb40-37103bf614ac.png](https://user-images.githubusercontent.com/40863240/126888349-12e03bec-3448-4bdf-bb40-37103bf614ac.png)

## DNS를 세팅해보자

먼저 우리는 DNS에 MX 타입 레코드를 등록해야 한다. 여기서 잠깐! MX 타입이란 무엇일까?

> MX 타입이란 `Mail eXchange` 타입을 의미한다. DNS에 있는 레코드 타입 중 하나이며, 메일이 수신될 위치를 결정해준다.

MX 레코드를 improvMx로 등록하게 되면 내가 사용하고 있는 도메인의 주소로 들어온 메일 서버는 improvMx가 되는 것이다. improvMx에서는 해당 메일을 받고 등록해둔 메일 주소로 보내주게 된다. 

다시 Vercel의 domains 탭으로 돌아와 DNS Records에 `Add Email Presets` 버튼을 클릭 후 ImprovMX를 클릭하면 친절하게도 추가해야 할 VALUE와 PRIORITY, TTL을 알려준다. 이렇게 MX 타입 레코드를 추가하는 것은 끝! 

![https://user-images.githubusercontent.com/40863240/126888826-f773b715-bff5-4314-bb37-ac00c0130144.png](https://user-images.githubusercontent.com/40863240/126888826-f773b715-bff5-4314-bb37-ac00c0130144.png)

다음으로 해야 할 일은, SPF 레코드 사용을 위해 TXT 타입 레코드를 추가하는 것이다. 여기서 잠깐! SPF 레코드는 무엇일까? 

> SPF(Sender Policy Framework) 레코드란 도메인에서 스팸을 발송하지 않도록 보호 해주는 TXT 레코드이다.

ImprovMX에서 알려준 TXT 레코드 까지 추가해주면 DNS 설정은 끝이다.  모든 설정이 끝나면 상태가 `Email Forwarding Active`로 활성화 되며 모든 세팅이 완료되었음을 알려준다. 

![https://user-images.githubusercontent.com/40863240/126889103-440fcc54-ef43-46ba-8c47-ce2b8637daa4.png](https://user-images.githubusercontent.com/40863240/126889103-440fcc54-ef43-46ba-8c47-ce2b8637daa4.png)

## 메일이 잘 들어오는지 테스트 해보자

위 사진에서 개인 메일 주소 옆의 `TEST` 버튼을 클릭해서 메일이 잘 도착하는지 확인할 수 있다.

![https://user-images.githubusercontent.com/40863240/126889174-c439ef60-a47b-447c-bf83-2a6d5aff448b.png](https://user-images.githubusercontent.com/40863240/126889174-c439ef60-a47b-447c-bf83-2a6d5aff448b.png)

여러개의 메일이 섞여서 확인하기 불편하다면 Gmail의 `필터 만들기` 옵션을 사용해서 설정한 도메인 주소로 들어온 메일만 라벨을 붙일 수 있다. 나의 사이드 프로젝트 이름은 `이야기 클럽` 이기 때문에 해당 도메인 주소로 들어온 이메일에는 이 라벨을 붙여주도록 설정하였다. 

![https://user-images.githubusercontent.com/40863240/126889240-e990b0b0-7fe3-4bdd-a0a5-5ba87a84ee6a.png](https://user-images.githubusercontent.com/40863240/126889240-e990b0b0-7fe3-4bdd-a0a5-5ba87a84ee6a.png)

테스트 했을 때 라벨이 잘 붙어서 들어오는 것을 확인할 수 있다.

![https://user-images.githubusercontent.com/40863240/126889285-661d96ab-6a1e-4dd6-9335-99d65a26014c.png](https://user-images.githubusercontent.com/40863240/126889285-661d96ab-6a1e-4dd6-9335-99d65a26014c.png)

## 마무리

직접 생성한 도메인에 알리아스를 생성하니 Gmail에서는 절대 불가능한 이름을 설정할 수 있다. `me@` 라든지 `contact@` 라든지 `suzie@` 라든지! 이미 사용된 이름이라는 말을 안봐도 된다고! 하하하! 재밌다. 그럼 이만.