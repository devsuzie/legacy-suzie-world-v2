---
title: 웹에서 파일 사용하기
description: 웹에서 파일을 업로드 하려면 어떻게 해야할까
date: 2020-08-26
tags:
  - Programming
layout: layouts/post.njk
------

일하던 중, 이미지를 업로드 해야 할 일이 생겼다. 서버에 파일을 업로드 할 때는 파일을 던져주기만 하면 서버가 저장하고 URL을 만들어주기 때문에 사실 클라이언트 단에서 해야 할 일은 많이 없다. 하지만 이미지 업로드에 대해 알아보다 보니 `blob`은 어떤 포맷인지, `FileReader`는 왜 쓰이는 건지, `binary data`는 무엇인지에 대한 궁금증이 생겼다. 그래서 쓰는 글!

## blob은 file의 포맷
타입이 file인 input 태그는 아래와 같다.
```js
<input type="file" />
```

여기서 file의 포맷이 바로 blob이다. Blob 객체는 파일류의 데이터를 나타내고, 텍스트와 이진 데이터의 형태로 읽을 수 있다. `ReadableStream`으로 변환 후 그 메서드를 사용해 데이터를 처리할 수 있다.

### blob의 메서드
- `Blob.arrayBuffer()`
  - Blob의 전체 내용을 이진 데이터로 담은 ArrayBuffer로 이행하는 Promise를 반환
- `Blob.slice()`
  - 메서드를 호출한 블롭의 바이트를 주어진 시작 바이트와 끝 바이트 범위에서 복제해 새로운 Blob 객체를 생성하고 반환
- `Blob.stream()`
  - Blob의 콘텐츠를 읽을 수 있는 ReadableStream을 반환
- `Blob.text()`
  - Blob의 전체 내용을 UTF-8 텍스트로 담은 USVString으로 이행하는 Promise를 반환 

> 참고: [Blob - Web API | MDN](https://developer.mozilla.org/ko/docs/Web/API/Blob)

## binary data와 base64
file의 포멧은 blob이고 blob은 이진 데이터 (binary data)의 형태로 읽을 수 있다.
2진수 데이터는 0과 1로 이루어진 데이터를 말하고, 모든 데이터와 파일은 binary data이다. 
이미지를 저장할 때에는 bytes(binary)로 저장해야 한다. 

이 저장된 binary data 이미지를 화면에 보이고 싶을 때에는 base64로 변환해주어야 한다.
JS에 내장 함수 중 `btoa`가 있는데 이를 사용하면 된다.
btoa 메서드는 이진 문자열(binary data)에서 base64로 인코딩된 ASCII 문자열을 생성한다. 또한 `atob()`을 사용하면 데이터를 다시 디코딩 할 수 있다.

> 참고: [WindowOrWorkerGlobalScope.btoa() - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/btoa)


## FileReader
`FileReader`를 사용하면 블롭에서 데이터를 읽을 수 있다. FileReader 객체는 File, 또는 Blob 객체를 이용해 파일의 내용을 읽고 컴퓨터에 저장하는 것을 가능하게 해준다. File 객체는 `input` 태그를 이용하여 유저가 선택한 파일들의 결과로 반환된 `FileList` 객체, 드래그 앤 드랍으로 반환된 `DataTransfer`객체로부터 얻는다.

```js
const reader = new FileReader();
reader.addEventListener('loadend', () => {
   // reader.result contains the contents of blob as a typed array
});
reader.readAsArrayBuffer(blob);
```

### FileReader 이벤트 핸들러
- `FileReader.onabor`
  - abort 이벤트의 핸들러. 읽기 동작이 중단 될 때마다 발생.
- `FileReader.onerror`
	- error 이벤트의 핸들러. 읽기 동작에 에러가 생길 때마다 발생.
- `FileReader.onload`
	- load 이벤트의 핸들러. 읽기 동작이 성공적으로 완료 되었을 때마다 발생.
- `FileReader.onloadstart`
	- loadstart 이벤트 핸들러. 읽기 동작이 실행 될 때마다 발생.
- `FileReader.onloadend`
	- loadend  이벤트 핸들러. 읽기 동작이 끝났을 때마다 발생.
- `FileReader.onprogress`
	-  progress 이벤트 핸들러. Blob컨텐트를 읽는 동안 호출된다.

> 참고: `FileReader`는  [EventTarget](https://developer.mozilla.org/ko/docs/Web/API/EventTarget)으로 부터 상속 받았기 때문에 언급된 모든 이벤트들은  [addEventListener](https://developer.mozilla.org/ko/docs/Web/API/EventTarget/addEventListener) 메소드를 사용하여 listen 하게 할 수 있다.

## 사용자가 선택한 이미지 보여주기
참고한 MDN [Using files from web applications](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) 문서에는 바닐라 자바스크립트로 작성한 예제가 있다.
나는 React Hooks를 사용해서 프로젝트를 작업하고 있기 때문에 내 멋대로 코드를 작성해봤다. 더 좋은 코드가 분명히 이 세상 어딘가에 존재할 것이니 참고만 해주시길…!

```js
import React, {useState} from 'react';

export default () => {
  const [file, setFile] = useState(null)
  const [url, setUrl] = useState("")

  const handleChange = (e) => {
    const selectedFile = e.target.files[0]
    setFile(selectedFile)

    const reader =  new FileReader();
    reader.onload = () => {
      setUrl(reader.result)
    };
    reader.readAsDataURL(selectedFile);
  }

  return (
    <div>
      <input type="file" onChange={handleChange}/>
      <img file={file} src={url} />
    </div>
  );
}
```
선택한 파일을 file state에 저장해서 이미지에 file 속성을 추가해줬다.
이 속성을 추가하게 되면 나중에 실제로 업로드를 위한 이미지를 fetch 할 수 있게 해준다고 한다.

새로운 `File Reader` 객체를 생성하고, `onload` 함수를 설정한다.
위에서 살펴본 것처럼 onload 이벤트는  읽기 동작이 성공적으로 완료되었을 때마다 발생한다.
성공적으로 읽어왔다면 URL 정보를 set 해주자.
set 한 URL을 이미지 태그의 src 속성에 설정해주면 된다.

이미지 파일의 전체 콘텐츠가 로드되었을 때, img 엘리먼트의 src 속성을 로드된 이미지로 설정하여 화면에 이미지가 나타나게 된다. 

## 마무리
이미지를 불러와 화면에서 섬네일을 확인하는 방법에 대해서 알아보았다. `blob`이 무엇인지, `FileReader`는 무엇인지에 대해서 알아보는 등 오랜만에 새로운 내용에 관해 공부해보니 즐거웠다. 일을 하면서 맨 처음 새로운 일을 할 때는 알지 못하는 미지의 영역에 대한 두려움이 컸다면, 지금은 처음 듣는 용어나 처음 해보는 작업을 하게 될 때 설레는 마음이 더 큰 것 같다. 어느 정도 문서를 보는 법과 모르는 것이 있을 때 어떻게 찾아 나가야 하는지 알게 돼서인 것 같기도 하다. 앞으로 내가 알아야 할 것들이 계속해서 생겨날 텐데, 즐거운 마음으로 학습할 수 있게 될 것 같아 기쁘다.