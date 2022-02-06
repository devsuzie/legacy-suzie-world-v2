---
title: JSON WEB TOKEN
description: JWT로 무엇을 할 수 있나 
date: 2021-12-05
tags:
  - Programming
layout: layouts/post.njk
------

nodejs를 공부하면서 간단한 로그인 기능을 구현해보게 되었다. 처음 내가 생각한 로그인 기능은 단순히 사용자의 데이터를 암호화 해서 DB에 저장하고 사용자가 로그인을 시도하면 로그인을 시도한 데이터와 암호화된 DB 데이터를 복호화해서 매칭하여 비교하면 된다고 생각했다. 그래서 두 정보가 동일하다면 success, 불일치라면 401을 뱉도록. 하지만 로그인 후에 보내는 API 요청에서는 어떻게 요청을 보낸 사용자가 로그인을 한 (인증이 완료된) 사용자인지 아닌지를 어떻게 알 수 있을까? 바로 이 때 JWT가 필요해진다. 

## JWT가 뭔데용

프로그래밍을 공부하면 행복한점이 공식 문서가 너무 잘 되어있다는거다. 어떤 강의나 블로그 글 보다도 더 최신화가 잘 되고 정확한건 바로 공식 문서를 읽어보는거다. [JWT](https://jwt.io/)는 무료 핸드북을 제공하고 있으니 읽어보면 좋을듯 하다. 물론 난 아직 읽지 않았다. 

그래서 JWT가 무엇일까? 이 글의 제목처럼 `JSON WEB TOKEN`의 약어이다. HMAC / RSA / ECDSA 암호화 알고리즘을 사용한 JSON 개체로 정의된 토큰이다. 이게 무슨 말이냐면... 우리의 데이터를 안전하게 송수신 할 수 있도록 도와주는 표준이라는 뜻이다.

> JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the `HMAC` algorithm) or a public/private key pair using `RSA` or `ECDSA`.
> 

## 언제 쓰는데용

서문에서 언급한 것 처럼, 로그인에 성공한 사용자가 로그인 이후에 API 요청을 보냈을 때 인증하기 위해서 이 토큰이 사용된다. 사용자가 로그인할 때 토큰 발급 받고, 다음 요청 보낼때 마다 이 토큰을 가지고 있다는것을 보여주면 된다. 어떻게 보여줄 수 있을까? 어디에 담아야 할까? JWT는 브라우저 스토리지는 보안이 취약하기 때문에 헤더에 담아서 보내라고 말하고 있다.

> You also [should not store sensitive session data in browser storage due to lack of security](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html#local-storage).
> 

## 어떻게 쓰는데용

### 로그인할 때 헤더에 토큰 저장하면서 사용해용

사용자가 로그인을 시도하면 `sign` 메서드를 사용해서 토큰을 생성한다. 파라미터로는 payload에 들어갈 데이터와 서버에서 지정한 (.env에 저장하거나 github secrets에 저장) 비밀 키를 넣으면 된다. 나는 `.env` 파일에 `TOKEN_SECRET` 이름으로 저장해두었다. 

```jsx
router.post('login', async (req, res) => {
	// DB에 login 정보와 일치하는 유저가 없는 경우 에러
  const user = await User.findOne({id: req.body.id});
  if (!user) return res.status(400).send('User not found');

  // 토큰 생성 후 Header에 Authorization 값으로 전달
  const token = jwt.sign({_id: user._id}, process.env.TOKEN_SECRET);
  res.header('Authorization', token).send(token);
});
```

토큰이 원하는 데이터와 함께 잘 생성되었는지 확인해보고 싶다면 생성한 토큰을 JWT 홈페이지에서 decoding 해볼 수 있다. Payload에 넣은 데이터가 잘 반영되었는지 Decoded PAYLOAD 섹션에서 확인할 수 있을 것이다. 데이터가 변경되면 토큰 값 또한 변경되고, 서버에서만 알고 있는 TOKEN 시크릿 키 또한 알고 있어야 유효한 토큰이 생성되기 때문에 훌륭한 보안성을 가지고 있다. 

![https://user-images.githubusercontent.com/40863240/144751447-de00f788-10b9-416a-9b2d-61b62fee5839.png](https://user-images.githubusercontent.com/40863240/144751447-de00f788-10b9-416a-9b2d-61b62fee5839.png)

### 로그인 후 API 통신할 때 저장된 토큰 검증하면서 사용해용

로그인 후 `/posts`라는 API 호출을 통해 데이터를 가져오고 싶다고 해보자. 서버는 이 요청을 보내는 사용자가 인증된 (로그인이 완료된) 사용자인지 알기 위해 토큰이 필요하다고 했다. 사용자가 로그인하면서 생성된 토큰을 헤더에 담아서 보내면 이 토큰을 가지고 토큰 비밀 키와 함께 유효성을 확인한다. 이 때 jwt의 `varified` 메서드 를 사용한다. 

```jsx
const verifyToken = (req, res, next) => {
	// 헤더로부터 토큰을 가져옴
  const token = req.header('Authorization');
	// 토큰이 없으면 401 에러
  if (!token) return res.status(401).send('Access Denied');

  try {
		// 유효한 토큰 값인지 확인 
    req.user = jwt.varified(token, process.env.TOKEN_SECRET);
    next();
  } catch (error) {
		// 유효하지 않은 토큰 값이면 400 에러
    res.status(400).send('Invalid Token');
  }
};
```

아래 코드는 posts GET API 호출을 하는 코드이다. express의  router.get() 에서 낯선 파라미터가 하나 보인다면 그것이 바로 방금전 우리가 작성한 verifyToken 함수를 사용한 미들웨어 파라미터이다. api endpoint와 콜백 함수 사이에 토큰을 검증하는 함수를 넣으면 끝이다. 테스트를 해보고 싶다면 포스트맨을 사용해서 헤더의 Authorization 값을 제거하고 /posts 요청을 날려보라. 유효하지 않은 토큰이라는 말과 함께 400 에러가 나타나는 것을 확인할 수 있을것이다.

```jsx
router.get('/posts', verifyToken, (req, res) => {
  res.json({
    posts: {title: 'title', content: 'content'},
  });
});
```

## 마무리

내용이 좀 딱딱한것 같아서 h2와 h3를 용용체로 써봤는데 어떨지 모르겠다. 서버 공부를 하면서 DB에 데이터는 어떻게 적재되는지, REST API 요청은 어떻게 이루어지는지 등을 알 수 있어서 흥미로운 요즘이다. 위 본문에 작성된 코드는 내가 nodejs를 공부하면서 만든 [깃헙 레포지토리](https://github.com/devsuzie/nodejs-playground)에서 확인할 수 있다. 사실 로그인 기능을 구현하기는 했지만 나중에 내가 프로젝트를 만들 때에는 이미 보안이 검증된 대기업 로그인 기능을 빌려쓸거다... 그럼 안녕 ~