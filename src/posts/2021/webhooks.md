---
title: IFTTT로 알아보는 webhook
description: webhooks 그 무궁무진한 세계...
date: 2021-01-31
tags:
  - Programming
layout: layouts/post.njk
------

회사에서 일을 하다 "설문 폼에서 설문을 완료했을 시 설문을 완료한 대상자를 어떻게 파악할 수 있을까?"라는 질문을 받았다. 설문 완료 대상자에 대한 정보를 슬랙 또는 메일로 전달받을 방법에 대한 것이었는데, 나는 아는 게 없는 1인이라서 개발자분들에게 여쭤봤고, 어떤 플랫폼이냐에 따라 다르지만 `webhook`을 통해 할 수 있을 것이라는 답변을 받았다. 이럴 수가! webhook이라면... github에서 특정 이벤트가 발생할 때마다 슬랙에서 알림을 받아볼 수 있도록 해주는 것이 아닌가? 이 webhook을 사용해 전혀 다른 것처럼 보이는 문제를 해결 할 수 있다는 말에 나는 webhook의 w도 모른다는 것을 인정하고 공부해보기로 결심했다. 이 글에서는 웹훅이 무엇인지, 그리고 웹훅을 사용해 깃헙 레포에 별⭐️이 눌렸을 경우 알림을 받는 방법에 대해서 알아보도록 하자.

## webhooks
웹훅은 웹앱의 특정 이벤트를 구독하고, 구독한 이벤트가 트리거 되었을 때 `HTTP POST`로 요청을 보낼 수 있게 해준다. 또 웹훅은 새 이벤트가 서버에서 발생한 경우 서버측 앱이 클라이언트 측 앱에 알릴 수 있도록 하는, 기존의 클라이언트가 서버를 호출하는 API와 다르기 때문에 역방향 API라고도 불린다. 순서를 파악하자면, `event`가 발생하고 `trigger`로 웹훅이 동작하면 `action`이 발생하는 것이다.

그렇다. 웹훅은 event driven programming이다. 리액트에서 중요하게 여겨지는 `Inversion Of Control`원칙과 같다. 리액트는 [React Hooks](https://reactjs.org/docs/hooks-intro.html)를 사용해 특정 라이프 사이클에서 코드가 실행 되도록 한다. 예를 들어 컴포넌트가 화면에 로드 되면 inversion Of Control이 일어난다. 코드를 나(개발자) 자신이 아닌 hooks가 부르는것이다. 
> 켄트형의 [Inversion Of Control](https://kentcdodds.com/blog/inversion-of-control) 블로그 글을 추천합니다.

웹훅도 동일하다. 특정 이벤트가 발생하면 (특정 라이프 사이클에서) 웹훅이 동작하고 (코드가 실행; POST 요청) 액션이 일어난다. 이제 웹훅에 대해 조금 알 것 같다. 그럼 이 웹훅을 사용해 볼 IFTTT란 무엇인지 알아보도록 하자. 

## no-code solutions
IFTTT는 `no-code solution`이다. [zapier](https://zapier.com/)나 [IFTTT](https://ifttt.com/)는 코드 작성 없이 앱을 만들 수 있게 해준다. 내가 가지고 있는 앱들을 연결해서 자동 워크플로우를 만드는 것이다. 사물 인터넷을 사용하는 방법과 비슷하다. 집에 들어오면 불이 켜지게 하는 것처럼 말이다. IFTTT가 의미하는 If This Then That 콤보의 반복이다. 

## IFTTT로 webhook 사용하기
웹훅과 IFTTT가 무엇인지에 대해서 알았다면 이제 본격적으로 시작해보자. 우선 [IFTTT](https://ifttt.com/)에 접속한다. 계정을 만들고 `create` 버튼을 클릭해서 Applet을 만들도록 하자. (참고로 IFTTT는 세 개의 Applet까지 무료로 제공한다) Create Applet 화면이 나왔다면 If This 옆의 작은 `add` 버튼을 클릭하면 된다.

![0](//images.ctfassets.net/5xgpncj4c37m/77YVFvibNS6GA1XJgbFshQ/50f893f8ce753a255f5d7e2de6fa65b0/1.png)

그러면 수많은 연결 가능한 서비스들이 나타나는데 우리는 그 중 `Webhooks`서비스를 선택하도록 하자. 

![2](//images.ctfassets.net/5xgpncj4c37m/3K7JZca9O5YKSDaeFvYioW/e9021e6847e64f24a0737fdaa1bf4142/2.png)

우리는 깃헙 레포가 별을 받게 되면 이벤트가 트리거 되도록 할 예정이니 이벤트의 이름을 `github_starred`로 이벤트 이름을 지정했다.

![3](//images.ctfassets.net/5xgpncj4c37m/3h5xYXFEtRrEylqW2my4e5/d768bf8923e83a35eb3a68fe2aa10fc4/3.png)

그러면 IF 부분은 완성이다! If receive a web request 다음 어떤 것을 실행시킬지 정하기 위해 Then That 옆의 `Add` 버튼을 클릭하자. 

![4](//images.ctfassets.net/5xgpncj4c37m/3OFlh6XfdkMKpxfeHXMK97/c4e93eafbdd9546f8d7bbb482f5f0525/4.png)

깃헙 레포가 별을 받게 되면 IFTTT앱에서 알림이 오도록 하기 위해 `Notification`서비스를 선택하면 이렇게 어떤 메시지를 통해 알림을 보여줄지 알려준다. `EventName`란에는 우리가 위에서 설정해준 이벤트 이름인 `github_starred`가 들어가게 될 것이다. 확인했다면 `Create action` 버튼을 클릭해 액션을 생성해준다.

![5](//images.ctfassets.net/5xgpncj4c37m/5QvnrEphJd9VxHG38i0UGU/6d97fe2649dd453f815663675333b66e/5.png)

우리의 첫번째 IFTT applet 완성! 멋진 콤보다. 

![6](//images.ctfassets.net/5xgpncj4c37m/6zIpeKWRLYIV73Lh4lgyIz/1dc18cbc7a78f69daa2bc5c5b715aecf/6.png)

이제 request URL 확인하기 위해 IFTTT에서 Webhooks의 `documentation` 버튼을 클릭한다. 문서에 들어가면 우리가 생성한 applet의 고유한 키값와 POST/GET request URL을 확인할 수 있다. 이제 이 URL을 터미널이나 브라우저에서 요청한다면 IFTTT앱으로부터 알림이 올 것이다. 확인해볼까요?

브라우저로 요청을 보내고...

![8](//images.ctfassets.net/5xgpncj4c37m/51N1WAsiso0izBNNdLTa4B/7c2779f218c5226768819cb0910cc880/8.png)

핸드폰을 확인하니 짠! 알림이 잘 왔다.

![10](//images.ctfassets.net/5xgpncj4c37m/5NzA343gqRTxpardgzrCoB/5c38fc9c00d6bde87d634a27b200588a/10.jpg)

이제 이 이벤트를 우리가 요청해서 보내는 것이 아니라, `github_starred`라는 이벤트의 이름에 걸맞게 github repo가 별을 받으면 알림이 올 수 있도록 github에게 위임해보자. 별을 받으면 알림이 오길 바라는 레포의 setting으로 가서 Webhooks 탭에 들어가 오른쪽 상단의 `Add webhook`버튼을 클릭하자.

![11](//images.ctfassets.net/5xgpncj4c37m/3elyE35OxdwsCSionLRq8K/4fa108b458f5fcdbce94f1f9abfdcd93/11.png)

github에서 제공하는 event를 구독하면 이 URL을 POST요청할 것이라고 친절히 알려준다. `payload URL`에 IFTTT에서 받아온 URL을 입력하고, Content type은 요모조모 사용하기 좋은 `json`으로 지정해줬다. 

![12](//images.ctfassets.net/5xgpncj4c37m/7riYfSRphuHq6Ewvn2aK7L/377456a53477cf31b7a08c5469592c82/12.png)

어떤 event를 통해서 webhook을 작동하게 할지 선택하는 섹션에서 아래처럼 `Stars`를 체크해줬다. 그리고 `Update webhook`을 통해 웹훅을 생성하면 완료!

![13](//images.ctfassets.net/5xgpncj4c37m/5y6zJsgvzaVmowBFW9CMMt/6b78592cd7aef1e163efd8e3f361fffc/13.png)

잘 실행되는지 확인하기 위해 아이유 선생님의 신곡 you are my celebrity~✨ 같은 느낌으로 내 레포에 내가 별을 눌러보았다. 그럼 짠! IFTTT를 통해 알림 메시지가 나타난다. 와아아. 

![10](//images.ctfassets.net/5xgpncj4c37m/5NzA343gqRTxpardgzrCoB/5c38fc9c00d6bde87d634a27b200588a/10.jpg)

똑똑한 github은 어떤 request/response를 전달 받았는지 바로 업데이트하여 우리에게 알려준다. Headers와 Payload를 확인해 어떤 유저가 나의 레포에 별을 주었으며 그의 github profile image까지 우리가 원하는 모든 것을 확인할 수 있다! 음하하.

![15](//images.ctfassets.net/5xgpncj4c37m/p4YHt7JSwKiQbogBhKQIe/e1d388eeec3bcbc326562ecd68595c18/15.png)

## 로컬에서 웹훅 개발하기
`no-code solution`으로 우리가 원하는 모든 것을 구현하지 못할 때가 있을 것이다. 그럴 때는 우리가 직접 핸들러를 작성해서 해결할 수 있다. IFTTT로 webhook을 사용하는 방법은 위에서 직접 해보았으니 로컬에서 webhook 사용하는 것은 상상코딩을 해보자! 절대 직접 하고 캡쳐하기 귀찮아서 그러는 게 아니다.

### 핸들러 작성
express를 사용해서 POST request를 만들 수 있을 것이다. `.env`에 IFTTT에서 가져온 handler URL을 설정하고 POST 요청을 보낼 수 있도록 로컬 웹 서버를 만들어보자. 아래와 같은 모습이 될 것이다.
```js
app.post("/github", (req, res) => {
  const content = "github starred event occurred";
  const imageUrl = "image_url";
  axios
    .post(process.env.IFTTT_WEBHOOK_URL, {
      content: content,
      embeds: [
        {
          image: {
            url: imageUrl,
          },
        },
      ],
    })
    .then((discordResponse) => {
      console.log("Success!");
      res.status(204).send();
    })
    .catch((err) => console.error());
});
```

### ngrok으로 확인해보자
우선 개발 환경에서 작동하는지 [ngrok](https://ngrok.com/)을 사용해서 테스트해보도록 하자. 
> 여기서 잠깐 토막 상식! `n`은 netwrok의 n을 뜻하고 `grok`은 이해한다는 뜻이라고 한다. 

서버를 실행시키고 있는 포트의 이름을 넣어 터미널에서 ngrok을 사용해보자. `./ngrok http {port_number}`를 입력하면 `forwarding https url`을 확인할 수 있을 것이다. ngrok을 사용해 받은 URL을 핸들러로 github webhooks의 payload url에 등록하면 우리가 등록한 이미지와 문구가 잘 나타날 것이다. (아마도)

### netlify로 확인해보자
우리가 계속해서 webhook을 동작하게 하려면 ngrok은 좋은 방법이 아니다. 컴퓨터를 끄면 우리가 ngrok을 사용해 전달 받은 url도 무용지물이 되기 때문이다. netlify를 통해 간단하게 운영환경에서도 webhook을 사용할 수 있다. 아래는 Netlify에서 발행한 [Build serverless functions with JavaScript](https://docs.netlify.com/functions/build-with-javascript/)를 참고하여 작성한 코드이다.

```js
exports.handler = async (event, context) => {
  try {
    const body = JSON.parse(event.body);
    const imageUrl = body.sender.image_url;
    const res = await axios.post(process.env.IFTTT_WEBHOOK_URL, {
      content: "github starred event occurred",
      embeds: [
        {
          image: {
            url: imageUrl,
          },
        },
      ],
    });
    console.log("Submitted!");
    return {
      statusCode: 204,
    };
  } catch (err) {
    return { statusCode: 500, body: err.toString() };
  }
};
```

너무 간단하다! function 배포를 할 때 Netlify에서 `.env` 설정을 해주는 것도 잊어서는 안된다.

## That's a wrap!
끝이다. 웹훅을 이용해서 전혀 다른 별개의 서비스를 연결해보았다. 웹훅의 세계는 정말 무궁무진하다. github, discord, slack뿐 아니라 Instagram, twitter 등 정말 많은 곳에서 제공하고 있다. 또 우리가 상상하는 그 어떤 것이든 이벤트 트리거가 될 수 있다는 것이 정말 매력적이다. 우리가 해야 할 것은 핸들러를 만들고 그 핸들러가 해야 할 일을 해야 할 시점에 하게 하는 것뿐이다. 이제 내가 이번 블로그 글을 마무리하면서 하고 싶은 말은... Generation Z처럼 해시태그로 말해본다. `#nocodemovement` `#zapiermakesyouhappier`