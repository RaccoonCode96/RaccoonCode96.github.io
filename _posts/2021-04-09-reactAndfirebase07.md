---
title: "20210409 React and Firebase07, img파일 dataUrl로 바꾸기, firebase에 img파일 업로드, FileReader API, Firesbase Storage, 랜덤id 생성 패키지(uuid),  Nomadcoder  " #제목
category: #카테고리
tag: #태그
  - React
  - Firebase
  - 트위터 클론
  - Nomadcoder
  - dataUrl
  - firebase Storage
  - uuid
  - 랜덤id
toc: true #옆에 목차
---

> # Nomadcoder

- [노마드코더 : 트위터 클론 수업](https://nomadcoders.co/nwitter)
- 노마드코더님의 강의를 들으면서 공부한 내용을 정리하는 용도로 작성되었으며, 본내용이 틀릴 수 도 있습니다.

<br>

> # Preivew Image (File Upload)

- firebase console의 storage 기능에서 bucket은 파일을 넣는 곳임
- 트윗할 때 사진을 첨부하기 위해서 트윗하는 방법을 바꿔야함

<br>

## Hooks

- `attachment` hook은 업로드하는 file를 표시할 수 있는 string 형식의 `dataURL` 값을 가짐
  - default 는 null 값으로 둠

```js
import Rweet from "components/Rweet";
import { dbService } from "fBase";
import React, { useEffect, useState } from "react";

const Home = ({ userObj }) => {
  const [rweet, setRweet] = useState("");
  const [rweets, setRweets] = useState([]);
  // A Hook for uploading a file
  const [attachment, setAttachment] = useState();
```

<br>

## Methods

- `onfileChange`함수는 file을 받는 input 태그에 file이 들어올 때 해당 event를 받아 해당 파일을 읽어 DataURL로 만들어 `attachment` hook에 값을 저장하는 기능을 가짐

  - [**<span style="color: blue">FileReader 개요</span>**](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) : 기본적으로 fileReader의 경우 비동기적으로 이루어짐
  - [**<span style="color: blue">Window: load event</span>**](https://developer.mozilla.org/en-US/docs/Web/API/Window/load_event) : `load` 이벤트는 전체 페이지가 load 되었을 때 또는 css파일, 이미지 등의 독립적인 resource가 load 되었을 때 발생함
  - [**<span style="color: blue">FileReader.onload</span>**](https://developer.mozilla.org/en-US/docs/Web/API/FileReader/onload) : window의 load 이벤트가 발생하고 끝났을 경우(즉, 이미지 같은 파일들이 load 될때 마다) 또는 raedAsArrayBuffer, readAsDataURL, readAsText와 같은 FileReader의 methods로 읽기가 가능할 때(성공 되었을 때만) callback이 실행. (그에 반해 onloadend는 성공하거나 실패하거나 상관 없이 실행됨)
  - [**<span style="color: blue">FileReader.readAsDataURL()</span>**](https://developer.mozilla.org/en-US/docs/Web/API/FileReader/readAsDataURL) : file을 읽어 DataURL로 encoding 함 read operation이 끝나고 readyState가 done이 되고 XHR의 loadend가 트리거 되었을 떄 reader의 result property 안에 데이터가 들어감

- **onload 부분에서 result 속성 값을 훅에 저장하게 됨**

<br>

- `onClearAttachment` : Attachment를 null 값으로 초기화 시켜주는 함수

```js
// Methods for uploading a file
const onFileChange = (event) => {
  const {
    target: { files },
  } = event;
  const theFile = files[0];
  const reader = new FileReader();
  reader.onload = (finishedEvent) => {
    const {
      currentTarget: { result },
    } = finishedEvent;
    setAttachment(result);
  };
  reader.readAsDataURL(theFile);
};

const onClearAttachment = () => {
  setAttachment(null);
};
```

<br>

## JSX

- [**<span style="color: blue">input tag : file type</span>**](https://developer.mozilla.org/ko/docs/Web/HTML/Element/Input/file)
  - `file` type의 input 태그를 넣으며 `accept` 속성을 통해서 image 파일만 가능하게 함 ([**<span style="color: blue">accept 값 입력 방식</span>**](https://developer.mozilla.org/ko/docs/Web/HTML/Element/Input/file#%EA%B3%A0%EC%9C%A0_%ED%8C%8C%EC%9D%BC_%EC%9C%A0%ED%98%95_%EC%A7%80%EC%A0%95%EC%9E%90)) 그래서 `image/*` 로 이미지 파일 전체라고 주었으며 onChange 이벤트에 `onFileChange` 이벤트 핸들러를 연결
  - **파일 업로드 성공시 preview 기능을 구현**하기 위해서 `attachment` 값이 성공적으로 들어 왔는지에 따라 조건을 걸어서 img와 clear 버튼이 들어 있는 div 태그를 보여주는지 결정함
  - img 태그에 src 속성에 성공적으로 받아진 `attachment` hook 값을 연결, clear 버튼에는 `attachment` hook 값을 null로 만드는 `onClearAttachment` 함수를 연결

```js
// JSX
  return (
    <div>
      <form onSubmit={onSubmit}>
        <input
          value={rweet}
          onChange={onChange}
          type="text"
          placeholder="What's on your mind?"
          maxLength={120}
        />
        <input type="file" accept="image/*" onChange={onFileChange} />
        <input type="submit" value="Rweet" />
        {attachment && (
          <div>
            <img src={attachment} alt="attachment" width="50px" height="50px" />
            <button onClick={onClearAttachment}>Clear</button>
          </div>
        )}
      </form>
      <div>
        {rweets.map((rweet) => (
          <Rweet
            key={rweet.id}
            rweetObj={rweet}
            isOwner={rweet.creatorId === userObj.uid}
          />
        ))}
      </div>
    </div>
  );
};

export default Home;
```

<br>
<br>
<br>

> # File Upload to firebase

- [why do we use firebase storage?](https://firebase.google.com/docs/storage/web/start) : data_url을 이미 가져왔는데 왜 firebase storage에 file을 올려서 download url을 만드는 것일까?
  - 단순 data_url을 바로 firebase firestore에 올려버리면 사용자가 해당 video 및 image를 공유하기 힘들기 때문이다. 그래서 storage에 올려 download url로 만들어서 firestore에 정보로 넣어주어야 공유하기 쉽다.

<br>

## firebase 환경 import 파일 설정 ()

```js
// fbase.js
import "firebase/storage";

export const storageService = firebase.storage();
```

<br>

## uuid 패키지 (랜덤 id 생성기)

- storage에 올릴 때 해당 파일 이름을 다르게 하기 위해서 사용
- [`npm install uuid`](https://www.npmjs.com/package/uuid) : 어떤 특별한 식별자를 랜덤으로 생성해줌
  - `import {v4 as uuidv4} from 'uuid';` , `uuidv4()`

<br>

## onSubmit Method 수정

- 전체적으로는 사용자가 이미지 파일을 첨부하는 것은 필수가 아니기 때문에 첨부하는 경우에만 storage에 저장하고 download url을 받아와 firestore에 트윗정보에 같이 추가 되어 올라가게 하는 것이다.
- 첨부파일의 data_url을 `attachment` hook이 관리하므로 `attachment` 형태에 따라서 조건문을 통해서 분류한다.

<br>

- storage의 구성은 store의 collection, document 처럼 폴더 파일 구성이다. 여기에서는 reference와 child라는 개념이 사용된다.
  - [Storage](https://firebase.google.com/docs/reference/js/firebase.storage.Storage)
    - [`ref ( path ? : string ) : Reference`](https://firebase.google.com/docs/reference/js/firebase.storage.Storage#ref) : ref method는 초기 경로 시작값을 설정할 path값을 받는다. (없으면 default : bucket root)
  - [Reference](https://firebase.google.com/docs/reference/js/firebase.storage.Reference) : storage의 reference obj로서 해당 obj를 delete, upload, 등을 할수 있다. (처음 시작할 때는 storage에서 `ref()` method를 통해서 reference incetance를 생성할 수 있다.), 그리고 `child()` method를 가지고 있는데 이를 통해서 reference obj에 상대적으로 하위 reference를 만들 수 있다.
    - [child()](https://firebase.google.com/docs/reference/js/firebase.storage.Reference#child) : ref와 비슷하게 경로를 받아서 해당 위치의 reference의 이름으로 설정하여 인스턴스를 만들 수 있다.
- `attachmentRef` 변수에 child를 통해서 user id 의 랜덤 id 명의 파일을 만들 수 있게 인스턴스화 하였다.

<br>

- 첨부된 파일을 업로드 하는 것은`put` 또는 `putString`을 통해서 첨부 가능하다. 하지만 우리는 data_url인 string 형식이기 때문에 `putString`을 사용한다.
  - ([putString ( data : string , format ? : StringFormat , metadata ? : UploadMetadata ) : UploadTask](https://firebase.google.com/docs/reference/js/firebase.storage.Reference#putstring)),
  - string으로 된 값의 data를 받고, string이 어떤 형식인지(StringFormat) 넣어주어야 한다.
  - [string format](https://firebase.google.com/docs/reference/js/firebase.storage#stringformat_1) , 여기에서 우린 data_url 로 넣었다.
- 이렇게 해서 업로드 하는 경우 반환하는 것은 uploadtask 로서 upload중의 process를 관리할 수 있게 해준다. promise이기 때문에 하위 method로 then을 설정할 수 있고 성공했을 경우 callback으로 uploadTastSnapshot이라는 것을 반환하는데 이를 `reponse` 변수에 할당해서 해당 위치의 reference를 기억하게 한다. 그리고 이는 업로드된 상태의 데이터를 가공할 수 있게 해준다. 받아오거나 등등... 하지만 download url은 막고 있어 `getDownloadURL()`함수를 사용할 수 있다.
- `getDownloadURL`은 reference를 통해서 직접적으로 사용할 수 있다. 그래서 아까의 `response`를 통해서 해당 위치의 reference에 접근하여 `getDownloadURL()`을 사용하여 `attachmentUrl` 변수에 할당하여 url을 받아 온다.
- 하지만 조건문을 걸었기 때문에 조건문 밖에서도 `attachmentUrl`변수를 사용하기 위해서 `let`을 통해서 선언 하였다. (scope issue)
- 그리하여 트윗 store에 submit 할 때 보내는 obj인 `rweetObj`에 `attachmentUrl` property를 추가하여 값을 반영시킨다. (attachmentUrl은 없어도 올라가야 하기 때문에 let 선언시 초기 값으로 `""`을 주었다.)

<br>

```js
import Rweet from "components/Rweet";
import { v4 as uuidv4 } from "uuid";
import { dbService, storageService } from "fBase";
import React, { useEffect, useState } from "react";

const onSubmit = async (event) => {
  event.preventDefault();
  let attachmentUrl = "";
  if (attachment !== "") {
    const attachmentRef = storageService
      .ref()
      .child(`${userObj.uid}/${uuidv4()}`);
    const response = await attachmentRef.putString(attachment, "data_url");
    attachmentUrl = await response.ref.getDownloadURL();
  }
  const rweetObj = {
    text: rweet,
    createdAt: Date.now(),
    creatorId: userObj.uid,
    attachmentUrl,
  };
  await dbService.collection("rweets").add(rweetObj);
  setRweet("");
  setAttachment("");
};
```

<br>

## JSX (Home component)

- 어차피 Rweet Component의 `rweetObj`로 `rweets`의 rweet인 아이템을 보내기 때문에 따로 url을 넣기위해 수정 필요 없이 같이 가게 됨

```js
<div>
  {rweets.map((rweet) => (
    <Rweet
      key={rweet.id}
      rweetObj={rweet}
      isOwner={rweet.creatorId === userObj.uid}
    />
  ))}
</div>
```

<br>

## JSX (Rweet component)

- editing false 일때의 JSX 부분에 text 밑에 attachmentUrl property의 유무에 따라서 img가 삽입되게 만들었다. img src에 해당 attachmentUrl을 연결한다.

```js
// Rweet Componet JSX
<h4>{rweetObj.text}</h4>;
{
  rweetObj.attachmentUrl && (
    <img
      src={rweetObj.attachmentUrl}
      alt="rweet img"
      width="50px"
      height="50px"
    />
  );
}
```
